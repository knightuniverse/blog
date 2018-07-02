---
title: Brotil
date: 2018-07-02 17:24:16
tags:
  - 技术
  - 读书笔记
---

### What Is Brotli Compression?

Named after a Swiss bakery product, Brotli is a relatively new compression algorithm released by Google back in 2015. According to Google, Brotli compression uses a combination of a modern variant of the [LZ77 algorithm](https://en.wikipedia.org/wiki/LZ77_and_LZ78), [Huffman coding](http://www.geeksforgeeks.org/greedy-algorithms-set-3-huffman-coding/) and 2nd order [context modeling](https://en.wikipedia.org/wiki/Context_model).

Google has performed various tests using the Brotli compression algorithm and has benchmarked the results against other modern compression algorithms. Based on [this study](http://www.gstatic.com/b/brotlidocs/brotli-2015-09-22.pdf), Google found that Brotli outperformed Zopfli (another modern compression algorithm) on average by 20-26% in terms of the compression ratio. When it comes to performance, having your files compressed to be smaller in size is always welcome.

### Install and Configure Brotli

Ubuntu 16.04 is the first Ubuntu distribution that allows you to install Brotli using apt-get. To do this simply run:

	apt-get update && apt install brotli


Once that is complete, you’ll need to install the [Nginx module for Brotli compression](https://github.com/google/ngx_brotli) and compile the latest version of Nginx (currently 1.13.0):

	git clone --recursive https://github.com/google/ngx_brotli ngx_brotli

	wget http://nginx.org/download/nginx-1.13.0.tar.gz
	tar zxvf nginx-1.13.0.tar.gz
	cd nginx-1.13.0

	./configure --add-module=../ngx_brotli
	make && make install


#### Brotli Settings

	brotli on;
	brotli_comp_level 3;
	brotli_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;


Since the nginx.conf file was modified, the last step is to reload Nginx. To do this, run the following command:

	systemctl reload nginx


### Pros vs Cons of Brotli

#### Pros


* Smaller compression results
* Faster load times
* Comparable compression times to Gzip


#### Cons


* Currently a bit cumbersome to adopt
* Non-universal browser support
* Manual configuration required to fully implement with WordPress


Additionally, Brotli can only be used over HTTPS, which can be seen as both a pro and a con. On one hand, it’s helping more sites move from HTTP to HTTPS, creating a more secure internet. While on the other hand, it introduces more work for those who want to enable Brotli but are still using HTTP.

### 原文地址

[Measuring the Effects of Brotli Compression on WordPress](https://www.sitepoint.com/brotli-compression-wordpress/)
