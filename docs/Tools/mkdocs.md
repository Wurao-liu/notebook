# MkDocs

有一些之后可能会用到的样式和用法，在这里作部分介绍，也记录部分使用方法备忘~

关于本站建立的原因和过程，可以看看[这里](../Life/about.md){target="_blank"}。

## 官方文档
在这里给出几个官方的文档链接，还是比较建议看看，能对于如何建立、修改这个站点有一个大致而全面的了解，便于之后自己配置。

- [MkDocs](https://www.mkdocs.org/){target="_blank"}

- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/ "主题样式"){target="_blank"}

??? tip "小提示~"

    如果遇到比较美观好看的样式，可以看看它的源码~

## 部分用法备忘
### Admonitions

这个是一个提示框的功能，可以用来强调一些内容，比如警告、注意等等。

#### 用法
1. **基础用法**

    当Markdown文件中写入如下内容时：

    ``` markdown
    !!! note

        这是最基础的用法。
    ```

    呈现效果如下：

    !!! note

        这是最基础的用法。

2. **带标题的用法**

    当Markdown文件中写入如下内容时：

    ``` markdown
    !!! note "这是一个标题"

        这是自定义标题。
    ```

    呈现效果如下：

    !!! note "这是一个标题"

        这是自定义标题。

3. **可以进行嵌套**

    ``` markdown

    !!! note "外部标题"

        这是外部内容。

        !!! note "这是一个嵌套标题"

            这是一个嵌套内容。
    ```

    呈现效果如下：

    !!! note "外部标题"

        这是外部内容。

        !!! note "这是一个嵌套标题"

            这是一个嵌套内容。

4. **省去图标和标题**

    ``` markdown
    !!! note ""

        这是一个省略图标和标题的提示框。
    ```

    呈现效果如下：

    !!! note ""

        这是一个省略图标和标题的提示框。

5. **可折叠块**

    === "初始不展开"

        ``` markdown
        ??? note "初始不展开"

            这是一个初始不展开折叠块的内容。
        ```

    === "初始展开"

        ``` markdown
        ???+ note "初始展开"

            这是一个初始展开折叠块的内容。
        ```

    呈现效果分别如下：

    ??? note "初始不展开"

        这是一个初始不展开折叠块的内容。

    ???+ note "初始展开"

        这是一个初始展开折叠块的内容。

6. **内联块**

    提示块也可以以行内块的形式呈现，使用`inline`+`end`将其放置在右侧，或仅使用`inline`将其放置在左侧：

    === "inline"

        !!! note inline "这是一个内联提示块"
            
            这是一个靠左侧的内联块。

        ``` markdown
        !!! note inline "这是一个内联提示块"
            
            这是一个靠左侧的内联块。
        ```
 
    === "inline-end"

        !!! note inline end "这是一个内联提示块"
                
            这是一个靠右侧的内联块。

        ``` markdown
        !!! note inline end "这是一个内联提示块"
                
            这是一个靠右侧的内联块。
        ```
    
    另外，要注意的是，需要先添加内联块（即进行申明），然后再添加内容。

#### 类型

???+ note

    !!! note

    !!! tip

    !!! info

    !!! abstract

    !!! success

    !!! question

    !!! warning

    !!! failure

    !!! danger

    !!! bug

    !!! example

    !!! quote
 
当然也可以修改`css`文件来自定义提示框的样式。

### Annotations

这个功能可以在文档中添加注释，可以用来解释一些内容，或者添加一些额外的信息。但同时，此功能与脚注功能有重复之处，只是观看起来更加方便一些。

#### 用法

``` markdown
这里是文本,(1)这里还是文本。
{ .annotate }

1. I'm an annotation! I can contain `code`, **formattedtext**, images, ... basically anything that can be expressed in Markdown.
```

呈现效果如下：

这里是文本,(1)这里还是文本。
{ .annotate }

1. I'm an annotation! I can contain `code`, **formattedtext**, images, ... basically anything that can be expressed in Markdown.

它也可以在其它类型元素中进行嵌套使用。

### Buttons

即使用`{.md-button}`进行声明，`#`中可填写链接，默认是该页面的一级标题;加上`.md-button--primary`表示填充按钮;当然，也可以在现有按钮中加入图标，其中，图标可以在[这里](https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/#search)[target="_blank"]查找。

``` markdown
[click here for more](#){ .md-button }

[click here for more](#){ .md-button .md-button--primary }

[Send :fontawesome-solid-paper-plane:](#){ .md-button }
```

呈现效果分别如下：

[click here for more](#){ .md-button }

[click here for more](#){ .md-button .md-button--primary }

[Send :fontawesome-solid-paper-plane:](#){ .md-button }

### Code blocks

使用三对反引号将代码包裹即可，也可以在第一对反引号后加上代码的语言，以实现代码高亮。

还可以注意以下几点：

- 添加标题：`title="标题"`；
- 添加和删除注释：使用对应语言的注释符号即可，如对于 C 语言，就是`// (1)`；若想删除注释记号，在括号后面添加`!`即可,如`// (1)!`；
- 控制行号：在语言名后面添加`linenums="1"`即可开始计数；
- 突出显示特定行：在语言名后面添加`hl_lines=想要突出显示的行号`即可，注意行号可以用空格间隔来实现高亮多行，也可以用`a-b`来控制高亮范围，如`python hl_lines="1 3 5"`、`python hl_lines="1-5"`；
- 内联代码块高亮：如`#!python range()`；

???+ example "代码块"

    ``` py title="bubble_sort.py" linenums="1" hl_lines="1 3 5"
    def bubble_sort(items): # (1)!
        for i in range(len(items)):
            for j in range(len(items) - 1 - i):
                if items[j] > items[j + 1]:
                    items[j], items[j + 1] = items[j + 1], items[j]
    ```

    1. 这是一个冒泡排序。

### Content tabs

1. 分组代码块

    ``` markdown
    === "C"

        ``` c
        #include <stdio.h>

        int main(void) {
            printf("Hello world!\n");
            return 0;
        }
        ```

    === "C++"

        ``` c++
        #include <iostream>

        int main(void) {
            std::cout << "Hello world!" << std::endl;
            return 0;
        }
        ```
    ```

    呈现效果如下：

    === "C"

        ``` c
        #include <stdio.h>

        int main(void) {
            printf("Hello world!\n");
            return 0;
        }
        ```

    === "C++"

        ``` c++
        #include <iostream>

        int main(void) {
            std::cout << "Hello world!" << std::endl;
            return 0;
        }
        ```

2. 其他内容分组

    ``` markdown
    === "Unordered list"

        * Sed sagittis eleifend rutrum
        * Donec vitae suscipit est
        * Nulla tempor lobortis orci

    === "Ordered list"

        1. Sed sagittis eleifend rutrum
        2. Donec vitae suscipit est
        3. Nulla tempor lobortis orci
    ```

    呈现效果如下：

    === "Unordered list"

        * Sed sagittis eleifend rutrum
        * Donec vitae suscipit est
        * Nulla tempor lobortis orci

    === "Ordered list"
    
        1. Sed sagittis eleifend rutrum
        2. Donec vitae suscipit est
        3. Nulla tempor lobortis orci

3. 当然也可以在提示块中进行嵌套使用：

    ???+ example "代码块"

        === "C"

            ``` c
            #include <stdio.h>

            int main(void) {
                printf("Hello world!\n");
                return 0;
            }
            ```

        === "C++"

            ``` c++
            #include <iostream>

            int main(void) {
                std::cout << "Hello world!" << std::endl;
                return 0;
            }
            ```

### Footnotes

用法与 Markdown 原生的脚注基本相同，但对于多行脚注，需要注意段落写在脚注下一行，且需要缩进。

``` markdown
Lorem ipsum[^1] dolor sit amet, consectetur adipiscing elit.[^2]

[^1]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.

[^2]:
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.
```

呈现效果如下：

Lorem ipsum[^1] dolor sit amet, consectetur adipiscing elit.[^2]

[^1]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.

[^2]:
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor massa, nec semper lorem quam in massa.

### Task lists

使用`- [ ]`表示未完成的任务，使用`- [x]`表示已完成的任务。

??? example inline end

    - [x] 这是一段已完成的任务
    - [ ] 这是一段未完成的任务 
        * [x] 这是一个已完成的子任务
        * [ ] 这是一个未完成的子任务 

``` markdown
- [x] 这是一段已完成的任务 
- [ ] 这是一段未完成的任务 
    * [x] 这是一个已完成的子任务
    * [x] 这是一个未完成的子任务
```

### Grids

可以将部分元素块排列成卡片网格

???+ quote "官方说明和示例"

    The list syntax is essentially a shortcut for card grids, and consists of an unordered (or ordered) list wrapped by a div with both, the grid and cards classes:

    <div class="grid cards" markdown>

    - :fontawesome-brands-html5: __HTML__ for content and structure
    - :fontawesome-brands-js: __JavaScript__ for interactivity
    - :fontawesome-brands-css3: __CSS__ for text running out of boxes
    - :fontawesome-brands-internet-explorer: __Internet Explorer__ ... huh?

    </div>


## 编写规范

我们在编写文章时，应当遵守一定的编写和排版规范，以便于提高视觉的美观性并获得较为舒适的体验。下面给出几个参考：

- [实验文档降压宝典](https://hypotensor.tonycrane.cc/){target="_blank"}；
- 编写链接时，应当尽量使用`[...](...){target="_blank"}`的形式，以便于在新标签页打开链接，而不是在原页面直接进行跳转而覆盖本页面，提高用户体验；