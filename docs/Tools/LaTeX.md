# $\LaTeX$
## 安装及使用
### 安装

对于环境的配置不是本文的重点，因此这里可能只会有极为简要的说明。

- 本地编译推荐安装 TexLive ；
  - 因为 TexLive 是全平台通用的；
- 安装 VScode ；
> 使用 VScode 的原因是可以使用 copilot 进行自动补全，提高编写效率。也可以使用 TeXworks ，这是 TeX Live 自带的编辑器，但是功能较为简单。
- 安装插件：LaTeX Workshop；
- 配置环境变量；

### Online

- [Overleaf](https://www.overleaf.com/){target="_blank"} 可以线上直接编译，不需要配置复杂的环境，还有各类模板可以使用，容易进行上手；

### 使用

- 新建文件，保存为 .tex ；
- 编写代码；
- 编译；
  - 可以直接在 VScode 中点击编译按钮，也可以使用命令行编译；
- 导出：导出后为排版好的 pdf 文件；

!!! warning "注意"

    对于带参考文献的文档，会有对文献的不同处理方式，此时要注意选择的编译器会有所区别，有较新的`biber`和较旧的`bibtex`，要注意在多次编译的时候选择合适的编译器。

### 发行版说明

对于各种不同的发行版和宏包管理不做说明，不是本次学习的重点，下面我们把重点放在$\LaTeX$的语法学习上。

### 命令及代码结构
- $\LaTeX$的命令以反斜杠`\`开头，后面跟着命令名，且对大小写敏感；
- 有些命令会对后续内容产生影响，可以用 {} 限定作用范围；
- 命令可以接收参数，[] 中为可选参数，{} 中为必选参数，逗号分隔。比如对于环境的命令：

```latex
\begin{环境名}[可选参数]{必选参数}
内容
\end{环境名}
```

- 注释：`%`后面的内容为注释，不会被编译；
- 空格：多个空格会被视为一个空格，多个或一个换行符会被视为一个空格；
- 段落：使用一个或多个空行来分隔段落；
- 文件结构：一个完整的LaTeX文档由以下几部分组成：

```latex
\documentclass{article}
% 导言区，调用宏包，定义命令，进行文档设置等
\begin{document}
% 正文区，文档内容
\end{document} % 结束文档
```

### 文档类与宏包

- 文档类：`documentclass`命令用于指定文档的类型，常用的文档类有：
  - `article`：用于短文，报告，论文等；
  - `report`：用于长篇报告，论文等；
  - `book`：用于书籍；
  - `letter`：用于信件；
  - `slides`：用于幻灯片；
  - 可以通过可选参数配置字号、纸张大小等，如`\documentclass[12pt,a4paper]{article}`；
- 宏包：`usepackage`命令用于调用宏包，宏包可以扩展LaTeX的功能，提供更多的命令和环境，相当于第三方库，如`\usepackage{amsmath}`用于调用数学公式宏包；
  - 使用：`\usepackage[options]{package}` or `\usepackage{package1，package2}`；
  - 常用宏包：TeXLive full中包含了大部分常用的宏包；
    - `amsmath`：数学公式；
    - `amssymb`：数学符号；
    - `geometry`：页面设置；
    - `graphicx`：插入图片；
    - `hyperref`：超链接；
    - `listings`：代码排版；
    - `xcolor`：颜色设置；
    - `fontspec`：字体设置；
    - `fancyhdr`：页眉页脚设置；
    - `titlesec`：修改章节标题`\chapter`、`\section`等的格式；
    - `tocbibind`：支持将目录、参考文献、索引本身写入目录项；
    - `titletoc`：修改目录中各条目的格式。类似宏包有tocloft等；
    - `enumitem`：修改列表环境enumerate 和 itemize 等的格式；
    - `algorithm2e`：较为复杂的、可定制的算法排版宏包；
    - `biblatex`：参考文献；
    - `xeCJK` & `ctex`：中文支持；
    - `lettrine`：段落首字母大写；
    - `indentfirst`：首行缩进；
    - ···宏包实在太多，可以用`texdoc 宏包名`查看具体各宏包文档的说明与介绍；

## 基础语法
### 段落特点及排版样式
#### 段落

- 连续的多个空白字符视为一个空格，行首的空格忽略；
- 行尾的换行符视为一个空格（中文语境除外）；
- 分段落；
    - 使用一个或多个空行分隔段落；
    - 行尾使用`\par`命令分隔段落；
  
#### 断行和断页；

- 断行：使用`\\`或者`newline`命令强制换行；
  - 需要注意这里的断行相当于段落内换行，不会产生新的段落；
  - 故而断行的下一行不会自动缩进；
- 断页：使用`\newpage`命令强制换页；
  - 也可以使用`\clearpage`命令；
  - 如果使用双栏，则两者有一定区别；
  
#### 字符和标点

- 转义字符：`\`后面跟% & # { }等，表示其相应的字符；`\textbackslash`表示`\`,因为`\\`是换行符；
- 标点符号（西文的一般会有一些特殊性，中文的一般比较正常）：
  - 单引号 ` '，双引号 `` ''；
  - 连字号 -，短破折号 --，长破折号 ---；
  - 省略号相比三个点更推荐`\ldots`；
- 防止西文连字；
  - 通过{}分隔即可避免连字，如`difficult dif{}f{}icult`；
  
#### 字体样式和字号

- 字体样式：
  - `\textbf{content}`：粗体；
  - `\textit{content}`：斜体；
  - `\texttt{content}`：等宽；
  - `\textsc{content}`：大写字符为更大的大写字母，小写字符为小的大写字母；
  - `\textsf{content}`：无衬线字体；
  - `\underline{content}`：下划线；
- 字号：一种形式的命令来修改字号 `{\tiny some text}`；
  - 从小到大：`\tiny`、`\scriptsize`、`\footnotesize`、`\small`、`\normalsize`、`\large`、`\Large`、`\LARGE`、`\huge`、`\Huge`；
- 字体：使用`fontspec`宏包，`\usepackage{fontspec}`，然后使用`\setmainfont{fontname}`，其中fontname可以是系统字体名，也可以是字体文件路径；
- 颜色：使用`xcolor`宏包，`\usepackage{xcolor}`，然后使用`\textcolor{color}{content}`，color可以是预定义的颜色，也可以是RGB值；

#### 强制行距/间距

- 长度单位：pt（point，1/72.27英寸）、mm（毫米）、cm（厘米）、in（英寸）、ex（当前字体x的高度）、em（当前字体M的宽度）；
- 行距：`\linespread{1.3}`，1.3倍行距，在导言区作设置（默认行距 1.2 倍字号大小）；
  - 局部修改要在后面加`\selectfont`命令，且要在范围内手动分段；
- 插入水平间距（多空格无效）：`hspace{length}`，如`\hspace{3em}`，3个字母M的宽度；
  - `\quad`：相当于`\hspace{1em}`，一个字母M的宽度；
  - `\qquad`：相当于`\hspace{2em}`，两个字母M的宽度；
  - `\hspace{\fill}`：防止因为换行而产生的空格,会填充剩余空间；
- 插入垂直间距：`vspace{length}`，如`\vspace{3em}`；
  - `\\[length]`：在换行的同时插入垂直间距，如`\\[1em]`；

??? note "try"

    ```latex
    {\linespread{2.0}\selectfont This is a paragraph with 2.0 linespread.\\
    This is the next line and \quad space and \hspace{3em} more and
    \hspace{\fill}lol\par}

    \vspace{-1em}Next paragraph.\\[1em] and next line.
    ```

#### 页边距和分栏

- 通过 geometry 宏包设置页边距；
  
```latex
\usepackage[left=1in,right=1in,top=1in,bottom=1in]{geometry}
\usepackage[margin=1in]{geometry} % 全相等
\usepackage[hmargin=1.5in,vmargin=1in]{geometry} % 水平和垂直
\usepackage[inner=1in,outer=1in]{geometry} % 内侧和外侧
\usepackage[margin=1in,includeheadfoot]{geometry} % 包括页眉页脚
\usepackage[margin=1in,showframe]{geometry} % 显示边框
```
```latex
\usepackage{geometry}
\geometry{left=1in,right=1in,top=1in,bottom=1in}
```

- 分栏：使用`twocolumn`文档类选项，或者使用`multicols`宏包；
  - 全局分栏：`\documentclass[twocolumn]{article}`，`\oneclumn`；
  - 局部分栏：`\begin{multicols}{2}`，`\end{multicols}`；
  
### 各种文档元素
#### 章节和目录

- 章节：`\chapter{title}`、`\section[别名]{title}`、`\subsection{title}`、`\subsubsection{title}`、`\paragraph{title}`、`\subparagraph{title}`；
  - 其中`\chapter`只能在book和report文档类中使用；
  - 可以加入可选参数表示短标题（显示在目录和页眉页脚中）；
  - 可以的带星号表示不编号（正常会带三级编号）；
  - 通过`\setcounter{secnumdepth}{n}`设置章节编号深度，n为数字，0为不编号；
- 使用`\tableofcontents`生成目录（新的一章/一节）；
  - 生成目录需要编译两次；
  - 可以通过`\addcontentsline{toc}{section}{name}`手动添加目录项；
- `\appendix`之后为附录，编号从A开始；

#### 生成标题页

- 一般在导言区设置信息；
  - `\title{title}`设置标题（必需）；
  - `\author{author}`设置作者（不写会警告），可以使用`\and`分隔多个作者，`\thanks{thanks}`添加脚注；
  - `\date{date}`设置日期（不写会自动生成当前日期）；
- `\maketitle` 生成标题页（一般在正文开始前，在bigin后）；
- `\titlepage` 生成独立的标题页；

#### 脚注

- 使用`\footnote{content}`生成脚注；
  - 脚注的编号是全局自动排序的；
- 表格环境中不能自动正确生成脚注，需要手动；
  - `\footnotemark`生成脚注编号；
  - `\footnotetext{content}`生成脚注内容；

??? note "try"

    ```latex
    some text\footnote{footnote}

    \begin{tabular}{l}
    \hline some text\footnotemark \\\hline
    \end{tabular}
    \footnotetext{footnote}
```

#### 图片

- 插入图片需要使用`graphicx`宏包；
- 使用`\includegraphics[options]{filename}`插入图片；
  - options：
    - width、height、scale、angle、origin、clip等；
  - filename：图片文件名，可以是相对路径或绝对路径；
    - 不能包含空格，建议全英文；
    - 可以省略后缀名，LaTeX会自动搜索；
- 图片环境：`figure`环境，可以使用`\caption{caption}`添加标题；
  - `\label{label}`添加标签，可以使用`\ref{label}`引用；
  - `\listoffigures`生成图片目录；
- 浮动体：LaTeX会自动调整图片位置，可以使用`h`、`t`、`b`、`p`、`!`等选项调整；
  - h：here，尽量在当前位置；
  - t：top，页面顶部；
  - b：bottom，页面底部；
  - p：page，单独一页；
  - !：忽略LaTeX的默认设置；
- `\graphicspath{{path1}{path2}...}`设置图片路径；
- `\centering`居中图片；
- 可以为graphicx或文档类设置draft选项；
  - 显示图片框架而不加载图片；
  - `\documentclass[draft]{article}` or `\usepackage[draft]{graphicx}`；

### 常用环境
#### 列表

- `itemize`无序列表，`enumerate`有序列表,`description`描述列表；
  - `\usepackage{enumitem}`可选 [(1)] [i.] [a)] 等指定编号格式；
  - `\setcounter{enumi}{i}`后下一个 item 从 i+1 开始编号；
- `\item`生成列表项，后接内容，`\item[marker]`可以自定义符号标签；
  - 使用`description`环境这标签会加粗左对齐表示关键字；

  ```latex
  \begin{description}
    \item[First] The first item
    \item[Second] The second item
  \end{description}
  ```

- 可以嵌套列表，最多四层；

??? note "try"


    ```latex
    \begin{itemize}
        \item First item
        \item[+] Second item
        \begin{enumerate}[i.]
            \setcounter{enumi}{2}
            \item First subitem
            \item Second subitem
        \end{enumerate}
    \end{itemize}
    ```

#### 对齐环境

- `flushleft`左对齐，`flushright`右对齐，`center`居中；
  - 这里的对齐环境是一个环境，不是一个命令；
  - 会在环境上下添加额外的垂直间距；
- `\raggedright`*左对齐*，`\raggedleft`*右对齐*，`\centering`居中；
  - 这里是命令，不是环境，会改变后面所有的文本对齐方式；
  - 不会在环境上下添加额外的垂直间距；

???+ tip "注意"

    这里`\raggedright`是左对齐，`\raggedleft`是右对齐，不要搞反了

??? note "try"


    ```latex
    \begin{center}some text\end{center}
    \begin{flushright}some text\end{flushright}

    \centering some text

    \raggedleft some text
    ```

#### 代码环境

- `verbatim`环境：原样输出，不解释命令；
  - 不能嵌套在其他环境中；
  - 可以使用`\verb`命令，输出行内代码，如`\verb|text|`；
    - 也可以使用`\verb*|text|`，*表示显示空格；
  - 可以使用`\begin{verbatim}...\end{verbatim}`输出多行代码； 
- `lstlisting`环境：生成高亮代码，代码排版，需要`listings`宏包；
  - `\begin{lstlisting}[options]`，`\end{lstlisting}`；
  - options：`language`、`basicstyle`、`numbers`、`frame`、`caption`等；
  - 可以使用`\lstinputlisting[options]{filename}`插入外部代码文件；
  - 可以使用`\lstinline[options]{code}`插入内联代码；

??? note "try"


    ```latex
    \begin{verbatim}
    #include <stdio.h>
    int main() {
        printf("Hello, world!\n");
        return 0;
    }
    \end{verbatim}

    \verb|\LaTeX| and \verb*|printf("Hello, world!\n");|
    ```

#### 表格

- 表格使用`tabular`环境，有时可以使用`array`环境提供辅助；
- 直接使用`tabular`环境会和文本混排，一般使用`table`环境包裹变成浮动体；
- `\begin{tabular}{cols}`，`\end{tabular}`，其中列格式 cols：
  - l：左对齐，c：居中，r：右对齐；
  - |：竖线分隔列；@{}：去除列间距；@{content}：用content替代默认的列间距；
  - p{width}：固定宽度，自动换行；m{width}：垂直居中；b{width}：底部对齐；
  - *{num}{cols}：重复num次cols列格式；
- 表格元素：`&`分隔列，`\\`分隔行，`\hline`添加水平线，`\cline{i-j}`添加i列-j列的水平线；
- booktabs宏包提供更好的水平线，`\toprule`、`\midrule`、`\bottomrule`、`\cmidrule{a-b}`；
- 可以使用[TablesGenerator](https://www.tablesgenerator.com/){target="_blank"}生成；

??? note "try"


    ```latex
    \begin{table}[h]
        \centering
        \begin{tabular}{@{}c|c|c|}
            \hline
            1 & 2 & 3 \\
            \hline
            4 & 5 & 6 \\
            \hline
        \end{tabular}
        \caption{A table} %生成表格序号，表格标题
    \end{table}
    ```

#### 浮动体

- 使得图片和表格脱离文本，独立寻找合适的位置排放；
- `figure`环境：插入图片；`\table`环境：包裹表格；

```latex
\begin{figure}[placement]
    ...
\end{figure}
```

  - placement：h (here), t (top), b(bottom), p(page), !（忽略LaTeX默认设置）；
  - 默认tbp，按h-t-b-p 顺序；
  - 限制包括：每页浮动体数量，占页面比例，浮动体间距等；
- 可以使用`\caption{caption}`添加标题（后可以接`\label{...}`用于引用）；
- 可以`\listoffigures` `\listofftables`生成目录；

#### 引用和标签

- 使用`\label{label}`为对象添加标签，使用`\ref{label}`引用,`\pageref{label}`引用页码；
- 可以使用`\label`记录的位置：
  - 章节标题后紧挨着使用，如`\section{title}\label{sec:label}`；
  - 行间公式中任意位置使用，如`\begin{equation}...\end{equation}\label{eq:label}`；
  - 有序列表每个item后使用，如`\item\label{item:label}`；
  - 浮动体环境中`\caption`后使用，如`\caption{caption}\label{fig:label}`；
  - 定理环境内部使用；
  - `\label`一般放在`\caption`后面；
  - 引用的编号会自动更新；
- 可以使用`hyperref`宏包，`\usepackage{hyperref}`，生成超链接；
- 不会记录编号的命令不可以使用（比如`\section*`）；

## 数学公式语法
### 数学模式

- 编写数学公式首先推荐载入`amsmath`宏包；
- 两种排版方式；
  - 行内公式，用一对`$`包裹，如`$a^2+b^2=c^2$`；
  - 行间公式，有下面几种方法：
    - equation环境，自动编号，`\begin{equation}...\end{equation}`，`\notag`或`\equation*`取消编号；
    - 一对`$$`包裹（不太推荐）或`\[...\]`包裹或displaymath环境，不编号；
    - 其他多行环境，如`align`、`multline`等；
- 数学模式相比文本模式的不同；
  - 字母变为斜体，字母当作变量处理，输入文本时使用`\text{}`命令；
  - 空格被忽略，可以使用`\quad`、`\qquad`等命令；
  - 不允许有空行，换行符被忽略，除多行环境外不能使用`\\`命令手动换行；

### 公式排版基础

- 直接写字母就表示变量，\开头是命令（命令后面不要加字母，建议加空格）；
- 空格均会忽略，需要手动空格；
  - `\`和`\:`表示一个小空格；
  - `\;`表示中等大小的空格；
  - `\ `(反斜杠加空格)是另一种插入空格的方式；
  - `\quad`表示一个较大的空格（大约是一个字符的宽度）；
  - `\qquad`表示一个很大的空格（大约是两个字符的宽度）；
  - `\!`是负空格，它会减少字符间的间距；
- 上下标使用 ^ 和 _，超过一个字符需要用 {} 包裹；
  - 有些时候也表示子式，比如 \sum \int 等，如：`\sum_{i=1}^{n} a_i`；
- 特定函数有命令时要使用专门的命令，比如`\sin` `\log` `\lim`等；
  - 没有的时候可以用`\mathrm{...}`包裹；
- 内部要穿插文字时使用`\text{...}`包裹；
- 有两种样式`\displaystyle`和`\textstyle`，即行间和行内，可以在不同情形下进行调整；
  - 例如`\sum`的上下标位置,`\int`（积分）的高度，`\frac`的分数样式等会有不同；
  - 也可以使用`\limits`和`\nolimits`改变上下标位置；

### 常用数学符号

- 太多了，可以参考[LaTeX数学符号表](https://www.latexlive.com/){target="_blank"}；
  
### 特殊数学字体

- 公式环境中不能使用`\textbf`等命令，可以使用`\mathbf`、`\mathit`、`\mathsf`、`\mathtt`等命令；
- 针对于数学环境中特定的命令：

|命令|样式|备注|
:--:|:--:|:--:|
|\mathbb{ABCDE}|$\mathbb{ABCDE}$|黑板粗体，只有大写，依赖 amssymb|
|\mathcal{ABCDE}|$\mathcal{ABCDE}$|花体，只有大写|
|\mathfrak{ABCDE}|$\mathfrak{ABCDE}$|Fraktur字体，依赖 amssymb|
|\mathscr{ABCDE}|$\mathscr{ABCDE}$|手写体，只有大写，依赖 mathrsfs|
|\mathsf{ABCDE}|$\mathsf{ABCDE}$|无衬线字体|
|\mathit{ABCDE}|$\mathit{ABCDE}$|意大利体|
|\mathbf{ABCDE}|$\mathbf{ABCDE}$|粗体,粗斜体使用 \boldsymbol|
|\boldsymbol{ABCDE}|$\boldsymbol{ABCDE}$|粗斜体|
|\mathrm{ABCDE}|$\mathrm{ABCDE}$|罗马体|
|\mathtt{ABCDE}|$\mathtt{ABCDE}$|等宽字体|

### 数学环境
#### align/aligned对齐环境

- 最常用align环境进行多行公式对齐；
- 用 `&` 分割或标记对齐位置，`\\`换行；

```latex
\begin{align}
a &= b + c  &     g &= h + i \\
  &= d + e + f  &   &= j\notag
\end{align}
```

- 用`\notag`取消编号，用`\nonumber`取消整个公式的编号,或使用`align*`环境；
- 可以使用`aligned`环境嵌套在其他环境中(如equation)进行对齐；
  - aligned环境本身并不会进入数学模式，不会自动编号，需要在数学模式中嵌套使用； 

#### 矩阵环境

- 可以使用内置的 array 环境，用法类似 tabular，左右需手动加括号；
- 推荐使用 amsmath 的矩阵环境；
  - matrix 不带定界符；
  - pmatrix 小括号；bmatrix 中括号；Bmatrix 大括号；
  - vmatrix 单竖线；Vmatrix 双竖线；
  - 写法同样 `&` 分隔，`\\`换行；

??? note "try"


    ````latex
    \[
    \mathbf{A} = \begin{bmatrix}
    a_{11} & a_{12} & \cdots & a_{1n}\\
    a_{21} & a_{22} & \cdots & a_{2n}\\
    \vdots & \vdots & \ddots & \vdots\\
    a_{n1} & a_{n2} & \cdots & a_{nn}\\
    \end{bmatrix}
    \]
    ```

### 规范写法

- 一般来说，公式中的字母都是变量，不是常数，所以应该使用斜体；
- 特定函数一定要用专门命令，或写为正体；
- 除了变量以外都要用正体，特别是微分算子；
- 括号括起来的内容不为一个字符高时要用`\left` `\right`；
  - ❌`(x^2)^2` $(x^2)^2$ ✅`\left(x^2\right)^2` $\left(x^2\right)^2$；
- 行间公式上方不加空行（不然行间公式算新一段落，会导致上方间距过大）；
- 一个锻炼写数学公式的网站：[TeXnique](https://texnique.xyz/){target="_blank"}；

## 更多
### 模板

- [ElegantLaTeX](https://github.com/ElegantLaTeX){target="_blank"}系列模板；
- beamer：用于制作幻灯片的模板；

### 参考网站

- [一份（不太）简短的 LaTeX2e 介绍](https://mirrors.ustc.edu.cn/CTAN/info/lshort/chinese/lshort-zh-cn.pdf){target="_blank"}；
- [一个清晰简单的说明](https://liam.page/2014/09/08/latex-introduction/){target="_blank"}；
- [xg的技能拾遗PPT！](https://slides.tonycrane.cc/PracticalSkillsTutorial/2023-spring-cs/lec4/#/){target="_blank"}；
- [LaTeX数学符号表](https://www.latexlive.com/){target="_blank"}；
- [支持手写识别和文档识别的公式编辑软件平台](https://simpletex.cn/){target="_blank"}；
- [简洁的LaTeX的数学公式速查手册](https://latex.emoryhuang.cn/){target="_blank"}；