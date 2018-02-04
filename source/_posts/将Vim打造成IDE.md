---
title: 将Vim打造成IDE
date: 2016-12-12 22:52:32
tags: 技术
---

从初次接触Vim，大概有两年的时间了。在软件开发中使用Vim，却只是这半年的时间。我对Vim的了解远远算不上深入，不过还是把自己使用vim的一些经验写出来，希望对Vim的用户以及感兴趣的人有所帮助。 

在使用Vim进行软件开发之前，我用的是微软的VisualStudio Code，我想大部分人都不陌生的一个编辑器。然而我之前一直在Windows或者Linux下使用，切换到了MacBook Pro之后，发现所有快捷键都要在重学一次；并且我对写两行代码都要频繁使用鼠标也感到了厌烦；而考虑到将来如果要部署服务，或者在服务器上做一些配置的调整，Vim还是要会。因此最后我决定认认真真学习使用Vim，现在Vim已经是我缺省的文本编辑器了，换用InteliJ或者VS Code不知为啥我反而总觉得别扭。大概是Vim中毒了吧。

如果你有一点儿Linux的底子，那我觉得你学Vim一定会非常容易。实际上你在Linux下习惯了文本界面之后，你会发现，Linux下很多的软件背后都有着一样的设计思路，反应到软件使用这一点上，你会发现参数的指定，命名啥的，几乎都有一个固定的模式。以Vim为例子，有很多操作都是[次数] [动作]这样的套路，比如拷贝两行代码的指令是这样的：

	2 yy


删除两行代码的指令是这样的：

	2 dd


这样很多事情其实就是一法通万法通，学一次就行。另外，Vim的最强大之处，在于在于它的可定制性。你完全可以定制出一个符合你自己编辑习惯的编辑器，在这样一个编辑器里，你的工作效率将达到最高。

初学者其实可以参考其他人的配置上手Vim，之后逐渐去调整形成适合自己风格的配置。另外现在也有个叫SpaceVim开箱即用的配置集合，大大降低了使用Vim作为IDE的门槛。

### 界面主窗口

![](file:///home/milo/Documents/MyDocs/Notes/2016/%E5%B0%86Vim%E6%89%93%E9%80%A0%E6%88%90IDE/pasted_image.png)

### 编译安装Vim

Bram Moolenaar 在写 Vim 时还是 90 年代初，至今已经 20 多年 过去了。后来巴西程序员 Thiago de Arruda Padilha（AKA Tarruda）向 Vim开源编辑器项目递交了两大补丁，对Vim的架构进行了大幅调整，结果遭到了Vim作者Bram Moolenaar的拒绝，因为对于Vim这样一个成熟的项目进行如此大的改变风险太高。于是Tarruda发起了Vim fork项目Neovim。

Vim的原作者Bram Moolenaar 因为 NeoVim火起来的趋势不得不勤劳更新自己的Vim，终于在2016年9月12日推出了Vim 8。Vim 8的新功能有：


* Asynchronous I/O support, channels, JSON
* Jobs
* Timers
* Partials, Lambdas and Closures
* Packages
* New style testing
* Viminfo merged by timestamp
* GTK+ 3 support
* MS-Windows DirectX support


Vim 8 的这次更新带来了更多可能性，让各种插件能够完成很多以前做不了的事情，让 Vim 在保持小巧的情况下，跟 emacs 一样变得 “像个操作系统了”，提供比以前好得多的体验。

编译安装步骤如下：

	git clone https://github.com/vim/vim.git 
	cd vim
	./configure --enable-pythoninterp=yes --enable-python3interp=yes --enable-xim --enable-fontset --enable-multibyte --enable-terminal
	make
	sudo make install


### Vim插件

#### Vundle

Vim插件管理工具，用来：


1. 安装插件
2. 更新插件
3. 清理没用的插件


还有一些其他的功能，详情还是阅读[GitHub上的文档](https://github.com/VundleVim/Vundle.vim)吧。

#### NERDTree

这就是左侧窗口用于浏览目录结构的插件。

	" NERD tree
	let NERDChristmasTree=0
	let NERDTreeWinSize=35
	let NERDTreeChDirMode=2
	let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
	let NERDTreeShowBookmarks=1
	let NERDTreeWinPos="left"
	" Automatically open a NERDTree if no files where specified
	autocmd vimenter * if !argc() | NERDTree | endif
	" Close vim if the only window left open is a NERDTree
	autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif
	" Open a NERDTree
	nmap <F5> :NERDTreeToggle<Enter>


#### YouCompleteMe

写代码少不了代码不全的功能。Vim插件中，最好用的代码不全插件应该非[YCM](https://github.com/Valloric/YouCompleteMe)了莫属了。

#### Universal-ctags

Tag文件(标签文件)无疑是开发人员的利器之一，有了tag文件的协助，你可以在Vim查看函数调用关系，类、结构、宏等的定义，可以在任意标签中跳转、返回。实际上Tag文件实现的就是现代IDE上的Go-To这个功能。

Tag文件本质上还是一个文本文件，它的每一行就是一条索引记录。每一行的格式是：

	{tagname}<Tab>{tagfile}<Tab>{tagaddress}[;"<Tab>{tagfield}..]


其中，

	;”


表示结束tagaddress，并且表示注释的起点。之所以这么做是为了兼容Vi（这软件的生命真长，估计真的只有爷爷辈的程序员还在用Vi了吧）。至于tagfield的格式，则是

	name:value


举个实际的例子说明吧，以下是我用Vim本身的代码库生成的ctags的几条记录：

	win_getid       src/window.c    /^win_getid(typval_T *argvars)$/;"      f       typeref:typename:int
	win_goto        src/window.c    /^win_goto(win_T *wp)$/;"       f       typeref:typename:void
	win_goto_hor    src/window.c    /^win_goto_hor($/;"     f       typeref:typename:void   file:
	win_goto_ver    src/window.c    /^win_goto_ver($/;"     f       typeref:typename:void   file:


之前在Linux系统上用的是[Exuberant Ctags](http://ctags.sourceforge.net/)。不过这个已经十分老旧了，而且很久没有更新了。后来Reza Jelveh把代码库从Sourceforge迁移到了GitHub上，[并且做了很多改进](http://docs.ctags.io/en/latest/news.html#importing-changes-from-exuberant-ctags)：


1. 重写了若干Parser： C/C++，Python，HTML
2. 添加若干新的Parser： Autoconf，Automake， CSS，JSON，ObjectiveC等等
3. 对一些旧的Parser进行了重大更新： PHP
4. 添加并且扩展了程序的配置参数
5. ...


Linux上需要编译安装。好在编译安装也不麻烦。要顺利编译安装Universal-ctags，需要依赖的的库有：


* autoconf
* automake
* pkg-config


之后就是照着官方文档介绍的步骤一步一步来了：

	$ ./autogen.sh
	$ ./configure --prefix=/where/you/want # defaults to /usr/local
	$ make
	$ make install # may require extra privileges depending on where to install


基本使用：

	ctags -R src/


#### Tagbar

这就是最右侧的类定义，类方法列表的窗口了。Tagbar并不负责管理Tag文件，它只显示当前编辑中的文件的相关符号。所以我们才需要使用Universal-ctags去维护Tag文件。安装也很容易，用Vundle安装就行。

### 我的Vim配置

最后还是贴出我自己的配置吧，方便学习参考：

	set nocompatible              " be iMproved, required
	filetype off                  " required
	
	" set the runtime path to include Vundle and initialize
	set rtp+=~/.vim/bundle/Vundle.vim
	call vundle#begin()
	" alternatively, pass a path where Vundle should install plugins
	
	" let Vundle manage Vundle, required
	Plugin 'VundleVim/Vundle.vim'
	
	Plugin 'mileszs/ack.vim'
	
	Plugin 'https://github.com/scrooloose/nerdtree.git'
	
	Plugin 'https://github.com/ctrlpvim/ctrlp.vim.git'
	
	Plugin 'https://github.com/Valloric/YouCompleteMe.git'
	
	Plugin 'https://github.com/tpope/vim-fugitive'
	
	Plugin 'vim-airline/vim-airline'
	
	Plugin 'vim-airline/vim-airline-themes'
	
	Plugin 'https://github.com/tpope/vim-surround.git'
	
	Plugin 'w0rp/ale'
	
	Plugin 'ryanoasis/vim-devicons'
	
	Plugin 'https://github.com/vim-scripts/vim-auto-save.git'
	
	" Track the engine.
	Plugin 'SirVer/ultisnips'
	
	" Snippets are separated from the engine. Add this if you want them:
	Plugin 'honza/vim-snippets'
	
	" one stop shop for vim colorschemes.
	Plugin 'flazz/vim-colorschemes'
	
	" prettier
	Plugin 'mitermayer/vim-prettier'
	
	" tagbar
	Plugin 'majutsushi/tagbar'
	
	" All of your Plugins must be added before the following line
	call vundle#end()            " required
	filetype plugin indent on    " required
	" To ignore plugin indent changes, instead use:
	"filetype plugin on
	"
	" Brief help
	" :PluginList       - lists configured plugins
	" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
	" :PluginSearch foo - searches for foo; append `!` to refresh local cache
	" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
	"
	" see :h vundle for more details or wiki for FAQ
	" Put your non-Plugin stuff after this line
	
	" our <leader> will be the space key
	let mapleader=" "
	
	" our <localleader> will be the '-' key
	let maplocalleader="-"
	
	" Split fast
	nnoremap <leader>\ :vs<CR>
	nnoremap <leader>- :sp<CR>
	
	" syntax highlight
	syntax enable
	set omnifunc=syntaxcomplete#Complete
	let g:solarized_termcolors=256
	colorscheme Tomorrow-Night
	
	" supported javascript libraries
	let g:used_javascript_libs='underscore,react,chai,requirejs,jasmine,d3'
	
	" Set syntax highlighting for specific file types
	autocmd BufRead,BufNewFile Appraisals set filetype=ruby
	autocmd BufRead,BufNewFile *.md set filetype=markdown
	
	" Backspace deletes like most programs in insert mode
	set backspace=2
	" Show the cursor position all the time
	set ruler
	" Display incomplete commands
	set showcmd
	" Set fileencodings
	set fileencodings=utf-8,bg18030,gbk,big5
	
	" Softtabs, 4 spaces
	set tabstop=2
	set shiftwidth=2
	set shiftround
	set expandtab
	
	" indent
	set autoindent
	set cindent
	
	" Display extra whitespace
	set list listchars=tab:--,trail:.,extends:>,precedes:<,space:·
	
	" Make it obvious where 80 characters is
	set textwidth=80
	set colorcolumn=+1
	
	" Numbers
	set number
	set numberwidth=5
	
	set matchpairs+=<:>
	
	" Ignore case when searching
	set ignorecase
	
	" When searching try to be smart about cases 
	set smartcase
	
	" Highlight search results
	set hlsearch
	
	" Highlight current line
	au WinEnter * set cursorcolumn
	au WinLeave * set nocursorcolumn
	set cursorline
	
	" NERD tree
	let NERDChristmasTree=0
	let NERDTreeWinSize=35
	let NERDTreeChDirMode=2
	let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
	let NERDTreeShowBookmarks=1
	let NERDTreeWinPos="left"
	" Close vim if the only window left open is a NERDTree
	autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif
	
	" Open a NERDTree
	nmap <C-b> :NERDTreeToggle<CR>
	
	" ctrap
	set wildignore+=*/tmp/*,*.so,*.swp,*.zip,*.png,*.jpg,*.jpeg,*.gif "
	" MacOSX/Linux
	let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn)$'
	if executable('ag')
	    " Use Ag over Grep
	    set grepprg=ag\ --nogroup\ --nocolor
	    " Use ag in CtrlP for listing files.
	    let g:ctrlp_user_command = 'ag %s -l --nocolor -g ""'
	    " Ag is fast enough that CtrlP doesn't need to cache
	    let g:ctrlp_use_caching = 0
	    let g:ackprg='ag --vimgrep'
	endif
	
	" Ack
	cnoreabbrev Ack Ack!
	nnoremap <Leader>a :Ack!<Space>
	
	set laststatus=2 " Always display the status line
	let g:airline#extensions#tabline#enabled = 1
	let g:airline_powerline_fonts = 1
	set statusline+=%{fugitive#statusline()} "  Git Hotness
	set statusline+=%{ALEGetStatusLine()} " ale
	
	nmap <Leader>r :RunInInteractiveShell<space>
	
	" ctrlp
	let g:ctrlp_map = '<c-p>'
	let g:ctrlp_cmd = 'CtrlP'
	let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn)$'
	
	" encoding
	set encoding=utf-8
	set termencoding=utf-8
	set fileencoding=utf-8
	set fileencodings=utf-8,ucs-bom,gbk,default,latin1
	
	" ale
	let g:ale_sign_column_always = 1
	let g:ale_statusline_format = ['⨉ %d', '⚠ %d', '⬥ ok']
	let g:ale_echo_msg_error_str = 'E'
	let g:ale_echo_msg_warning_str = 'W'
	let g:ale_statusline_format = ['⨉ %d', '⚠ %d', '⬥ ok']
	let g:ale_echo_msg_format = '[%linter%] %s [%severity%]'
	let g:ale_linters = {'js': ['eslint']}
	
	" vim-devicons
	set guifont=Droid\ Sans\ Mono\ for\ Powerline\ Plus\ Nerd\ File\ Types:h11
	" loading the plugin 
	let g:webdevicons_enable = 1
	" adding the flags to NERDTree
	let g:webdevicons_enable_nerdtree = 1
	" Force extra padding in NERDTree so that the filetype icons line up vertically
	let g:WebDevIconsNerdTreeGitPluginForceVAlign = 1
	" adding to vim-airline's statusline 
	let g:webdevicons_enable_airline_statusline = 1
	
	" Prettier
	nnoremap <Leader>f :PrettierAsync<Enter>
	
	" tagbar
	nnoremap <F8> :TagbarToggle<Enter>
	
	" tag {ident}
	nnoremap <Leader>] <C-]>
	nnoremap <Leader>[ :po<Enter>
	
	" misc
	nnoremap <Enter> G
	
	" Trigger configuration. Do not use <tab> if you use https://github.com/Valloric/YouCompleteMe.
	let g:UltiSnipsExpandTrigger='<Leader>pp'
	" If you want :UltiSnipsEdit to split your window.
	let g:UltiSnipsEditSplit="vertical"
	
	" vim-auto-save
	let g:auto_save = 1  " enable AutoSave on Vim startup
	let g:auto_save_in_insert_mode = 0  " do not save while in insert mode
	
	" Tab navigation like Firefox.
	nnoremap <C-S-tab> :tabprevious<CR>
	nnoremap <C-tab> :tabnext<CR>
	nnoremap <C-t>     :tabnew<CR>
	
	nnoremap <leader>1 :1tabnext<CR>
	nnoremap <leader>2 :2tabnext<CR>
	nnoremap <leader>3 :3tabnext<CR>
	nnoremap <leader>4 :4tabnext<CR>
	nnoremap <leader>5 :5tabnext<CR>
	nnoremap <leader>6 :6tabnext<CR>
	nnoremap <leader>7 :7tabnext<CR>
	nnoremap <leader>8 :8tabnext<CR>
	nnoremap <leader>9 :9tabnext<CR>
	nnoremap <leader>0 :tablast<CR>
	
	if has('nvim')
	    let s:editor_root=expand("~/.config/nvim")
	else
	    let s:editor_root=expand("~/.vim")
	endif
	
	


参考文章
----


1. [将你的Vim 打造成轻巧强大的IDE](http://www.open-open.com/lib/view/open1429884437588.html)
2. [Using Vim as a JavaScript IDE](http://www.dotnetsurfers.com/blog/2016/02/08/using-vim-as-a-javascript-ide)
3. [在 Vim 上安装使用 Tagbar](http://ju.outofmemory.cn/entry/26918)
4. [Must-Have Vim JavaScript Setup](http://www.panozzaj.com/blog/2015/08/28/must-have-vim-javascript-setup/)
5. [Using ctags on modern Javascript](https://dance.computer.dance/posts/2015/04/using-ctags-on-modern-javascript.html)
6. [VIM configuration for happy Java coding](http://www.lucianofiandesio.com/vim-configuration-for-happy-java-coding)
7. [vi/vim使用进阶](https://blog.easwy.com/archives/advanced-vim-skills-catalog/)
8. [Universal Ctags](https://ctags.io/)
9. [提高 Vim 使用效率的 12 个技巧](http://blog.jobbole.com/87481/)
