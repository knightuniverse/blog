---
title: BNF
date: 2018-01-30 12:59:13
tags: 技术
---

定义
--

巴科斯范式(BNF: Backus-Naur Form 的缩写)是由 John Backus 和 Peter Naur 首先引入的用来描述计算机语言语法的符号集。现在，几乎每一位新编程语言书籍的作者都使用巴科斯范式来定义编程语言的语法规则。 BNF也包含了一些拓展版本，比如有标准文档的EBNF和ABNF。

在BNF中，双引号中的字("word")代表着这些字符本身。而double_quote用来代表双引号。 

在双引号外的字（有可能有下划线）代表着语法部分。 

< > : 内包含的为必选项。 

* ☐ : 内包含的为可选项。 

{ } : 内包含的为可重复0至无数次的项。 
|  : 表示在其左右两边任选一项，相当于"OR"的意思。 
::= : 是“被定义为”的意思 
"..." : 术语符号 
[...] : 选项，最多出现一次 
{...} : 重复项，任意次数，包括 0 次 
(...) : 分组 
|   : 并列选项，只能选一个 
斜体字: 参数，在其它地方有解释 

下面是是用BNF来定义的Java语言中的For语句的实例：

	FOR_STATEMENT ::= 
	      "for" "(" ( variable_declaration | 
	  ( expression ";" ) | ";" ) 
	      [ expression ] ";" 
	      [ expression ] ";" 
	      ")" statement


参考链接
----


1. [语法规范：BNF与ABNF](http://kb.cnblogs.com/page/189566/)
2. [Backus–Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)
3. [The language of languages](http://matt.might.net/articles/grammars-bnf-ebnf/)
4. [Notations for context-free grammars](http://www.cs.man.ac.uk/~pjj/bnf/bnf.html)
5. [Augmented BNF for Syntax Specifications: ABNF](ftp://ftp.rfc-editor.org/in-notes/rfc4234.txt) 
6. [BNF and EBNF: What are they and how do they work?](http://www.garshol.priv.no/download/text/bnf.html#id2.3.)
7. [Translational Backus–Naur form](https://en.wikipedia.org/wiki/Translational_Backus%E2%80%93Naur_form)
8. [Extended Backus–Naur form](https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form)

