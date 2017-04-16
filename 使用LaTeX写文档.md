[TOC]
### 导言

> 平时工作也一直在用LaTeX，也一直想写一篇入门的文档。最近也在写产品手册，正好也在使用LaTeX撰写，所以写一篇介绍基本用法的文章，以供大家了解。

#### 一、LaTeX简单介绍

LaTeX是一个文档处理的宏包，只要你选择了对应的文档格式，设置好具体的页面排版方式，后续专注于写作内容即可。相比较Word这种所见即所得的文字处理软件，LaTeX的一个特点就是在撰写文档的时候，不知道具体的文档生成的样式（但你可以估计到生成的格式）。

标准的LaTeX文档是以.tex结尾的纯文档文本，所以其跨平台性特别好。一个写好的tex文档，在任何平台编译出来效果是完全一致的。而word、page文档等，在不同平台不同的软件下显示的结果是不一样的，所以其跨平台性很差。而且，纯文本文档有个好处就是文件小，并且通过一些管理工具非常容易管理。而且编辑tex的软件很多，不需要专有软件才能编辑。

LaTeX对数学公式的支持非常优秀，因此被广大的科技工作者、大学生用于撰写科技文章或者专业的论文，国外的学术杂志甚至只接受LaTeX的论文文档。

LaTeX是一系列宏包集合，因此一个标准的TeX文档应该是命令和文字的结合。

#### 二、安装和使用

LaTeX是一个排版系统，因此需要安装这个系统，其下载地址为：

* [Windows](http://www.tug.org/texlive)
* [MacOS](http://www.tug.org/mactex/)
* [Linux]()

建议Windows用户使用TeXLive，MacOS用户使用MacTeX（当然如果你是高级用户了可以尝试Basic版本的MacTeX，占用空间小）。Linux用户可以下载TeX的软件包，也可以用系统带的包管理器下载。（比如Debian和Ubuntu可以使用apt-get install，Fedora可以使用yum，Arch用户可以使用pacman等）

编辑文档的工具可以有很多种，最简单的是记事本（但肯定很难用）。一般Windows建议使用[WinEdt](http://www.winedt.com/),Linux用户可以使用[LyX](http://www.lyx.org/)这一个可以预览的TeX编辑软件（几乎等同于所见即所得了），如果熟练了用户可以使用[Sublime Text+LaTeXtools](http://www.sublimetext.com/)以及[GNU Emacs](https://www.gnu.org/software/emacs/)。

#### 三、入门的简单文档

**1、TeX文档的基本框架**

```LaTeX
\documentclass[a4paper,11pt]{article}  %声明文档类型为article
\begin{document}   % 声明文档开始
	This is a test document for \LaTeX .   %文档内容
\end{document}	% 文档结束
```
首先要声明文档的样式，LaTeX支持多种文档类型，一般常用的是book、article、report三种。不同的文档类型有不同的结构。"%"后面的内容是注释，不会在编译后生成的文档中显示。

文档的内容都在\begin{document}和\end{document}之间，\begin{document}表示声明文档内容开始，\end{document}表示文档结束。

**2、支持中文**

LaTeX支持中文的方式有两中，一个使用CJK包然后声明一个CJK环境。另外一个用xunicode使用unicode字符支持中文。本文介绍第二种方式（我不喜欢CJK）。

示例内容：

```LaTeX
\documentclass[a4paper,11pt]{article}
\usepackage{fontspec, xunicode, xltxtra}

\begin{document}
	This is a test document for \LaTeX . \\
	这是一个\LaTeX 中文样本文档。\\
\end{document}	
```
编译后发现内容如下：

![文档内容](http://7viihf.com1.z0.glb.clouddn.com/test.png)

中文不能正常显示，原因在于字体的设置，因此需要重新设置字体，如果只是简单的使用一种字体，那么可以如下处理：

```LaTeX
\documentclass[a4paper,11pt]{article}
\usepackage{fontspec, xunicode, xltxtra}
\setmainfont{MicrosoftYaHei}  
\XeTeXlinebreaklocale "zh"  %中文的断句
\XeTeXlinebreakskip = 0pt plus 1pt minus 0.1pt

\begin{document}
	This is a test document for \LaTeX . \\[10pt]
	这是一个\LaTeX 中文样本文档。\\
\end{document}	
```

特别注意，中文的断句方式和英文是不一样的（英语是自动断句），因此需要声明中文的断句方式为"zh"。\\\\[10pt]表示换行，并且下一行和上一行行距为10pt。

生成的文档如下：

![中文文档内容](http://7viihf.com1.z0.glb.clouddn.com/chinese.png)


#### 四、更进一步

**1、\\usepackage[option]{package}**

LaTeX很多功能需要宏包提供，所谓宏包就是一个文件提供了很多功能用于作者调用。宏包的调用命令如下：

```LaTeX
\usepackage[colorlinks,citecolor=green]{hyperef}  %使用超链接宏包
```

举例如下：

```LaTeX
\documentclass[12pt,a4paper,openany,fleqn]{book} %声明文档类型
\usepackage{fontspec, xunicode, xltxtra} %使用xunicode相关宏包
\usepackage{tikz} %使用tikz宏包画图
\usepackage{subfig}  %使用subfig宏包
\usepackage{color} %使用color宏包调用颜色
\usepackage{graphicx} %使用graphicx处理图片
\usepackage{titlesec} %使用titlesec定义章节标题等
\usepackage{indentfirst} %首行缩进处理
\usepackage{geometry}  %使用geomtry包设置页面
\usepackage[colorlinks,linkcolor=black,anchorcolor=blue,citecolor=green]{hyperref} %使用超链接
\usepackage{wrapfig} %使用wrapfig处理浮动图片
\usepackage[fleqn]{amsmath} %使用amsmath处理公式
\usepackage{latexsym}
\usepackage{booktabs}
```

**2、图片处理**

我们写文档时，很多时候都需要添加图片至文档，LaTeX处理图片一般有很多宏包，用的最多的就是graphicx宏包。使用方式如下：

```LaTeX
\documentclass[a4paper,11pt]{article}
\usepackage{fontspec, xunicode, xltxtra}
\usepackage{graphicx}  %要加载graphicx宏包

\setmainfont{MicrosoftYaHei}  
\XeTeXlinebreaklocale "zh"  %中文的断句
\XeTeXlinebreakskip = 0pt plus 1pt minus 0.1pt

\begin{document}
	This is a test document for \LaTeX . \\[10pt]
	这是一个\LaTeX 中文样本文档。\\
	这个是广发基金的Logo图片。\\
	\begin{figure}[htbp!]
	\centering  %图片居中
	\includegraphics[width=\textwidth]{picture/guangfa.jpg}
	\label{fig:gflogo}
	\caption{广发基金LOGO}
	\end{figure}
\end{document}	
```

编译后生成的文档截图如图所示：
![图片处理的文档截图](http://7viihf.com1.z0.glb.clouddn.com/tupian.png)

\\centering是居中的命令，另外也可以用\\begin{center}文档内容\\end{center}居中环境来处理文档居中。

**3、表格处理**

除了图片，我们很多时候还要处理表格。表格可以使用宏包来处理。本示例使用booktabs宏包处理，示例代码如下：

```LaTeX
\documentclass[a4paper,11pt]{article}
\usepackage{fontspec, xunicode, xltxtra}
\usepackage{booktabs}

\setmainfont{MicrosoftYaHei}  
\XeTeXlinebreaklocale "zh"  %中文的断句
\XeTeXlinebreakskip = 0pt plus 1pt minus 0.1pt

\begin{document}
%处理表格
\begin{table}[htbp]
\caption{人员表}
\label{tab:function}
\begin{tabular*}{\textwidth}{@{\extracolsep{\fill}}lcc}
	\toprule %-------------------------------------
	姓名                 &性别                &年龄  \\
	\midrule  %------------------------------------
	张三                 &男                  &20   \\
	李四                 &男                  &32   \\
	李倩                 &女                  &25   \\
	\bottomrule  %---------------------------------        
\end{tabular*}
\end{table}
\end{document}	

```
生成的表格图示如下：

![表格内容图片](http://7viihf.com1.z0.glb.clouddn.com/table.png)



#### 五、总结

LaTeX有很多其它的功能，特别是在编写大型文档或者书籍时效率很高。包括写公式、引用、参考文献处理，非常有用。

附上最近写的产品文档的源文件地址：
[汇添富企业版产品手册源文档](https://github.com/allenmagic/qybsc)
[汇添富企业版产品手册PDF文件](https://github.com/allenmagic/qybsc/blob/master/mannual.pdf)

更多的功能，请后续关注。



