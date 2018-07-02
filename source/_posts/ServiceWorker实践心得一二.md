---
title: ServiceWorker实践心得一二
date: 2018-07-02 19:21:29
tags:
  - 技术
---

### 背景

我司现在的API是以微服务的模式进行设计的，也就是说，前端一个页面可能需要和好几个不同的API进行交互。刚好有这么一个页面，需要加载几千条数据。这几千条数据同时又要去调用另外一个API获取另外一部分数据。

简单说，就是这个页面的数据分成了好几部分，分散在了两个不同的API上。数据量一大，并发的API请求就多了起来，造成了数据下载慢，页面渲染卡顿等问题，极大的降低了用户体验。

我思考过的解决方案有这么几种：


1. 使用NodeJS编写一个中间层API，这个API负责组装数据。实际上这就是[服务端BFF](https://segmentfault.com/a/1190000009558309)的思路了
2. 前端利用ServiceWorker，把这一类的重I/O操作转移到ServiceWorker内。也就是前端实现BFF。


这两种方案都是基于BFF架构，无非是在哪个地方实现的问题。之所以只能用BFF模式，是因为如果我们在基层的微服务API调用其他API，很容易造成API依赖混乱，为将来API治理带来极大的混乱和风险。

基于一下考虑，我决定还是在ServiceWorker内实现BFF：


1. 目前只有一个页面需要优化
2. 单独一个NodeJS中间层代价还是太大了，并且直觉上并没有任何优势，不过有个优点就是负载全都在服务端，客户端压力很小
3. Service Worker日后我们可以利用CacheAPI，IndexedDB实现一些特定的缓存策略
4. 要能直接复用原有的代码，基础设施（这一点直接排除了利用新的协议，比如Web Socket之类的问题，不过原本适用场景确实也是不同的）


### Service Worker生命周期

参考这篇文章： [The Service Worker Lifecycle](https://bitsofco.de/the-service-worker-lifecycle/)

### 技术思路

#### 现有技术背景

React + Redux

#### 需要解决的问题


1. 复用Redux相关的actions
2. 所有的I/O调用都转移到了Service Worker，那么就需要保存原先Redux的middlerware的现场，在Service Worker结果返回之后，恢复现场，然后再调用Redux的Dispatch更新全局States


第一个问题很好解决，第二个问题则稍微困难了点。通过本次Service Worker，我发现Redux实际上还挺恶心的，因为Redux实际上只是提供了一套规则，然而极其恶心的状态维护和管理，甚至异步请求等都需要开发着自己实现。

回到正题，解决第二个问题，实际上也很简单。这就要求我们自定义一个Redux middleware，对于每一个Action，我们都生成一个唯一的GUID，把上下文相关的函数封装一个Task对象。待Servcie Worker返回之后，取出这个Task对象，然后进行下一步操作。

#### Redux middleware

	class TaskQueue {
	  constructor() {
	    this.queue = {};
	  }

	  getTask(guid) {
	    return this.queue[guid];
	  }

	  enqueue(task) {
	    this.queue[task.guid] = task;
	  }

	  dequeue(guid) {
	    delete this.queue[guid];
	  }

	  flushAll() {
	    const guids = Object.keys(this.queue);
	    each(guids, id => delete this.queue[id]);
	  }
	}

	const workerTaskQueue = new TaskQueue();

	function sendMessage(message) {
	  return new Promise((resolve, reject) => {
	    const sw = navigator.serviceWorker.controller;
	    const guid = uuid();
	    const task = {
	      guid: uuid(),
	      resolve,
	      reject,
	    };

	    workerTaskQueue.enqueue(task);

	    sw.postMessage({
	      guid: task.guid,
	      body: message,
	    });
	  });
	}

	function messageEventHandler(eventArgs) {
	  const { guid, body } = eventArgs.data;
	  const task = workerTaskQueue.getTask(guid);
	  const { status, response } = body;
	  const { resolve, reject } = task;

	  if (status !== PROMISE_STATUS.FULFILLED) {
	    reject(response);
	  } else {
	    resolve(response);
	  }

	  workerTaskQueue.dequeue(guid);
	}

	export function bindSWMessageEvent() {
	  navigator.serviceWorker.addEventListener('message', messageEventHandler);
	}

	export function unbindSWMessageEvent() {
	  navigator.serviceWorker.removeEventListener('message', messageEventHandler);
	}


	export default ({ dispatch, getState }) => next => action => {
	  if (typeof action === 'function') {
	    return action(dispatch, getState, extraArgument);
	  }

	  const API = action[CALL_API];
	  if (typeof API === 'undefined') {
	    return next(action);
	  }

	  const { types, uri, method, nonce } = JQAPI;
	  const body = API.body || {};
	  const [requestType, successType, failureType] = types;
	  next(
	    assign({}, action, {
	      type: requestType,
	    }),
	  );

	  function handleRequestSucc(response) {
	    return next(
	      assign({}, action, {
	        response,
	        type: successType,
	      }),
	    );
	  }

	  function handleError(error) {
	    if (error.code === 'nonce.error') {
	      global.localStorage.removeItem('nonce');
	    }

	    return next(
	      assign({}, action, {
	        type: failureType,
	        code: error.code || 'unknown.error',
	        message: error.message || 'unknown.error',
	      }),
	    );
	  }

	  return sendMessage({
	    accessToken: getAccessToken(),
	    uri,
	    method,
	    body,
	    action: requestType,
	  }).then(handleRequestSucc, handleError);
	};


#### Service Worker

##### Register

	import initServiceWorker from './serviceWorker';

	initServiceWorker(global.self);


##### initServiceWorker

	import process from './nest';

	export default function initServiceWorker(sw) {
	  sw.addEventListener('activate', eventArgs => {
	    eventArgs.waitUntil(sw.clients.claim());
	  });

	  sw.addEventListener('message', function(eventArgs) {
	    const { source, data: task } = eventArgs;
	    if (task) {
	      task.outbound = source;
	      process(task);
	    }
	  });
	}


##### Action Handler Router

我们接收所有的Task，然后根据Action.type转发处理。不同的Action.type转发到不同的Handler处理。这里，对于每一个Handler都会有一个duty的字段，用这个字段来筛选出用于处理该Action.type的Handler。

	import _ from 'lodash';
	import * as Bees from './bees';

	const beesMap = new Map();
	_.each(Object.values(Bees), bee => {
	  beesMap.set(bee.duty, bee);
	});

	export default function process(task) {
	  const { body } = task;
	  const { action } = body;
	  const beeCtor = !beesMap.has(action)
	    ? beesMap.get('PROXY')
	    : beesMap.get(action);
	  const bee = new beeCtor();

	  return bee.process(task);
	}



##### Example Handler

	export default class ScheduleCalendar {
	  static duty = Rooms.FETCH_SW_MODE;

	  fetchRooms({ accessToken, uri, method, body }) {
	  }

	  fetchTeachers(guid, accessToken, rooms) {
	  }

	  merge(rooms, teachers) {
	    const promise = new Promise((resolve, reject) => {
	      const teacherMap = new Map();

	      _.each(teachers, item => {
	        teacherMap.set(item.id, item);
	      });

	      _.each(rooms, room => {
	        room.teacher = teacherMap.get(room.teacherId);
	      });

	      resolve(rooms);
	    });

	    return promise;
	  }

	  process(task) {
	    const me = this;
	    const { guid, body: requestParams, outbound } = task;
	    const { accessToken, uri, method, body } = requestParams;

	    me.fetchRooms(requestParams).then(
	      rooms => {
	        return me.fetchTeachers(guid, accessToken, rooms).then(mergedRooms => {
	          outbound.postMessage({
	            guid,
	            body: {
	              response: mergedRooms,
	              status: PROMISE_STATUS.FULFILLED,
	            },
	          });
	        });
	      },
	      response => {
	        outbound.postMessage({
	          guid,
	          body: {
	            response,
	            status: PROMISE_STATUS.REJECTED,
	          },
	        });
	      },
	    );
	  }
	}



总结
--

此次优化，目标页面性能确实有了10左右的提升，数据加载和渲染时间明显缩短，用户体验还不错。就结果而言，可喜可贺。

利用Service Worker在前端实现BFF，算是比较特别的一种用法了。之前我甚至想过利用Shared Service Worker + Service Worker，实现一套[Actor并发模型](https://en.wikipedia.org/wiki/Actor_model)，算得上是异想天开了。不过这个真的可以当作日后的练习，练练手。

### 参考


* [Service Workers: an Introduction](https://developers.google.com/web/fundamentals/primers/service-workers/)
* [Activate Service Workers Faster](https://davidwalsh.name/service-worker-claim)
* [The Service Worker Lifecycle](https://bitsofco.de/the-service-worker-lifecycle/)


