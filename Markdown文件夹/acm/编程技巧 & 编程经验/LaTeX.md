# $\LaTeX$

- 发行版的基本作用是提供 TEX 后台处理机制和命令行程序，用户还需 要前台编辑器来编辑源文件。即可以将MikTeX理解为编译器，TeXstudio理解为编辑器。

- 共三种语句：命令 (command) 、数据和注释 (comment) 。命令又分为普通命令和环境 (environment) 。普通命令以\起始，环境则包括一对起始声明和结尾声明。

- 导言页用来完成一些设置，比如指定文档类型，引入宏包，定义命令、环境等；文档的实际内容则放在正文部分。

  > `\documentclass[options]{class} % 文档类声明` `\usepackage[options]{package} % 引入宏包 ...` `\begin{document} % 正文 ... \end{document}`

[参考文档](http://dralpha.altervista.org/zh/tech/lnotes2.pdf)