# HTML

!!! warning "注意"

    由于整体内容比较多，并且本人也是初学，所以主要是面向「自己」来书写，主要是写一些个人认为需要注意、记录的东西～

??? note "引入"

    个人是先学了 markdown 语言，但是经过我们在不同场景的使用，我们发现 markdown 其原生语法可能有时候并不能很好地支持我们想要的样子，比如说：对图片位置的安排、图片的大小、图片底下的标题... 那么这个时候我们就需要用 HTML 来在其中进行嵌入，从而达到我们想要的样子。

    从这里我们就可以感受到，这是一个功能很强大的语言，能够直接调整我们所看到的样式形状等。所以我们应当学习一些 HTML，来帮助我们更好地认识这些事物。

    事实上，我们去再仔细了解一下 markdown 语言，我们便会发现，markdown 语言的本质，实际上是一种 ==标记语言==，是对 HTML 的一种简化，所以对 HTML 的了解显得更加有必要了。

## 基础知识

一般来说，一个 HTML 元素主要有下面几个部分：

- Opening tag：开始标签，包含元素的名称（如: `p`），被左、右角括号所包围。
- Content：元素的内容。
- Closing tag：结束标签，包含元素的名称（如: `/p`），被左、右角括号所包围。

但是并不是所有元素都拥有完整的这三部分，一些元素只有一个标签，通常用来在此元素所在位置插入/嵌入一些东西。这些元素被称为**空元素**。例如：元素 `<img>` 是用来在页面插入一张指定的图片。

标签内除了元素名以外，还可以指定元素的 **属性** (attribute)，属性是用来描述元素的附加信息，通常以 name="value" 的形式出现，以空格来连接。属性可以在开始标签中指定。在这里，我们可以不使用引号，也可以使用单引号或是双引号，但两者不能混搭。为了保持一致性和准确性，我们应该使用引号，个人倾向于使用双引号。

那么在这里，我们就会遇到实体引用的问题了。实体引用是用来表示一些特殊字符的，比如说：`&`、`<`、`>`、`"`、`'`，这些字符是 HTML 语法自身的一部分，所以我们不能直接使用它们，那如果我们在文本内需要这个字符呢？我们可以使用实体引用来代替它们，与上面的五个特殊字符相对应的替换是：`&amp;`、`&lt;`、`&gt;`、`&quot;`、`apos`。

??? example

    `<p>HTML 中用 <p> 来定义段落元素。</p>`

    `<p>HTML 中用 &lt;p&gt; 来定义段落元素</p>`

    效果分别如下所示：

    <p>HTML 中用 <p> 来定义段落元素。</p>

    <p>HTML 中用 &lt;p&gt; 来定义段落元素</p>

    第二个例子中，`<p>` 被替换成了 `&lt;p&gt;`，这样就不会被浏览器当作 HTML 代码来解析了，是我们想要的效果。


例如：`My <a href="https://zjuers.com/index.html target="_blank" title="ZJU 轻首页">favourite website</a>`，其中 `href`、`target` 和 `title` 都是属性。

有些属性没有值，或者它只有一个与属性名称相同的值，这种属性被称为布尔属性。这两种形式共存，方便起见就写没有值的属性。如：`<input type="text" disabled />` 和 `<input type="text" disabled="disabled" />` 是等价的，方便起见，我们写前一种形式。

允许进行元素之间的嵌套，但是要注意打开和关闭的顺序要对应起来。

元素主要分为两个类别：

- 块级元素：块级元素在页面中以块的形式展现。在其 tag 前后的内容会被自动换行。
- 内联元素：内联元素通常出现在块级元素中并环绕文档内容的一小部分，而不是一整个段落或者一组内容。

另外，注释为 `<!-- comment -->`。

??? success "一个简单的完整 HTML 文本"

    ```html
    <!doctype html>
    <html lang="zh-CN">
        <head>
            <meta charset="utf-8" />
            <title>我的测试站点</title>
        </head>
        <body>
            <p>这是我的页面</p>
        </body>
    </html>
    ```

    其中 `<!doctype html>` 是文档类型声明；`<html>` 是根元素，这个元素包裹了页面中所有的内容；`<head>` 这个元素是一个容器，它包含了所有你想包含在 HTML 页面中但不在 HTML 页面中显示的内容。这些内容包括你想在搜索结果中出现的关键字和页面描述、CSS 样式、字符集声明等等。`body` 元素包含了所有你想在页面中显示的内容。

## HTML 元信息

在页面加载完成的时候，HTML 文档中的头部是不会显示在 web 浏览器的。但是头部中的内容，也就是说用标签 `<head>` `</head>` 中包裹的内容，会影响到页面的显示效果，里面包含了很重要的信息，我们现在来了解了解吧～

### 标题

`<title>` 元素定义了页面的标题，标题会显示在浏览器的标题栏中。

### 元数据

`<meta>` 元素定义了文档的元数据，元数据是关于 HTML 文档的数据。

- `<meta charset="utf-8">`：定义了文档的字符集，通常使用 UTF-8。

许多 `<meta>` 元素包含了 `name` 和 `content` 属性：

- `name` 指定了 `meta` 元素的类型；说明该元素包含了什么类型的信息；
- `content` 指定了实际的元数据内容；

比如说：

- `<meta name="description" content="对本页面的描述">`：定义了页面的描述；
- `<meta name="author" content="作者">`：定义了页面的作者；
- ...

页面的描述和标题都可以在搜索引擎中显示出来，所以我们可以通过这两个元素来提高页面的 SEO（搜索引擎优化）效果。

### 自定义图标

favicon (favourite icon) 可以作为网页图标，通常显示在浏览器的标签页上。我们可以使用 `<link>` 元素来定义 favicon。

页面添加网页图标的方式有：

1. 将其保存在与网站的索引页面相同的目录中，以 `.ico` 格式保存（大多数浏览器支持更通用的格式，如 `.gif` 或 `.png`）;

2. 将以下行添加到 HTML 的 `<head>` 块中以引用它：

```html
<link rel="icon" href="favicon.ico" type="image/x-icon">
```

其中 `rel` 属性定义了链接的关系；`href` 属性定义了图标的路径；`type` 属性定义了图标的类型，如果我们使用的是 PNG 格式的图标，我们可以将 `type` 属性设置为 `image/png`。

### 在 HTML 中应用 CSS 和 JavaScript

我们使用 CSS 来让网页更加炫酷，并使用 JavaScript 来让网页有交互功能，比如视频播放器以及更多功能。这些应用在网页中很常见，它们分别使用 `<link>` 元素以及 `<script>` 元素。

- CSS：`<link rel="stylesheet" href="style.css">`，`rel` 属性定义了链接的关系，`href` 属性定义了 CSS 文件的路径；
- JavaScript：`<script src="script.js" defer></script>`，`src` 属性定义了 JavaScript 文件的路径，最好加上 `defer` 以告诉浏览器在解析完成 HTML 后再加载 JavaScript。这样可以确保在加载脚本之前浏览器已经解析了所有的 HTML 内容。这样你就不会因为 JavaScript 试图访问页面上不存在的 HTML 元素而产生错误。也可以直接在 `<script>` 元素中编写 JavaScript 代码，比如说：

```html
<script>
    alert("Hello World!"); 
</script>
```

> 但是或许统一将代码放到外部文件中会比较整齐。

### 为文档设定主语言

设定好语言有助于更好地被搜索。

我们可以像上面的例子一样，在 `<html>` 元素中使用 `lang` 属性来定义文档的主语言。比如说：

```html
<html lang="zh-CN">
</html>
```

我们也可以将文章分段设定为不同语言，比如说：

```html
<p>Chinese example: <span lang="zh-CN">一个例子</span>.</p>
```

## 行文内容
### 标题和段落

标题元素 `<h1>` 到 `<h6>` 用来定义标题，`<h1>` 是最大的标题，尽量一个页面只用一个一级标题，同时标题层级不宜过多，尽量限制在三级及以内。我们可以使用它们来组织页面的结构。段落元素 `<p>` 用来定义段落。两者都是块级元素，段落元素在其前后会自动换行。

### 内联强调

- `<em>` 元素：斜体，有强调语义；
- `<strong>` 元素：粗体，有强调语义；
- `<small>` 元素：小号字体；
- `<i>` 元素：斜体；
- `<b>` 元素：粗体；
- `<u>` 元素：下划线；
- `<abbr>` 元素：使用 `<abbr>` 标签，里面的内容为缩略语，在网页中用下划虚线标注，鼠标光标悬停在上面就会显示设置好的完整解释（在 `title` 属性指出）

!!! example "缩略语"

    === "HTML 代码"

        ```html
        <p>
            第 33 届<abbr title="夏季奥林匹克运动会">奥运会</abbr>已于 2024 年 7 月在法国巴黎举行。
        </p>
        ```

    === "效果"

        第 33 届<abbr title="夏季奥林匹克运动会">奥运会</abbr>已于 2024 年 7 月在法国巴黎举行。

- `<mark>` 元素：高亮文本；
- `<sup>` `<sub>` 元素：分别对应上标和下标；
- 代码块相关元素：
    - `<code>` 元素：将文本渲染为代码块（块级、行内均可）；
    - `<pre>` 元素：保留空白字符（文本编辑器上有多少空白，网页就显示多少空白，常用于代码块）；
    - `<var>` 元素：标记变量名；
    - `<kbd>` 元素：标记键盘输入；
    - `<samp>` 元素：标记计算机程序输出；

!!! example "代码块"

    === "HTML 代码"

        ```html
        <pre><code>const para = document.querySelector('p');

        para.onclick = function() {
            alert('Owww, stop poking me!');
        }</code></pre>

        <p>
            You shouldn't use presentational elements like <code>&lt;font&gt;</code> and <code>&lt;center&gt;</code>.
        </p>

        <p>
            In the above JavaScript example, <var>para</var> represents a paragraph element.
        </p>

        <p>Select all the text with <kbd>Ctrl</kbd>/<kbd>Cmd</kbd> + <kbd>A</kbd>.</p>

        <pre>$ <kbd>ping mozilla.org</kbd>
        <samp>PING mozilla.org (63.245.215.20): 56 data bytes
        64 bytes from 63.245.215.20: icmp_seq=0 ttl=40 time=158.233 ms</samp></pre>
        ```

    === "效果"

        <pre><code>const para = document.querySelector('p');

        para.onclick = function() {
            alert('Owww, stop poking me!');
        }</code></pre>

        You shouldn't use presentational elements like <code>&lt;font&gt;</code> and <code>&lt;center&gt;</code>.
        
        In the above JavaScript example, <var>para</var> represents a paragraph element.
        
        Select all the text with <kbd>Ctrl</kbd>/<kbd>Cmd</kbd> + <kbd>A</kbd>.

        <pre>$ <kbd>ping mozilla.org</kbd>
        <samp>PING mozilla.org (63.245.215.20): 56 data bytes
        64 bytes from 63.245.215.20: icmp_seq=0 ttl=40 time=158.233 ms</samp></pre>

那么，我们也就可以理解此时在本页面上，用简化语法写出来的代码框，其对应的实质性的 HTML 代码是什么了。

### 列表

!!! example "列表"

    === "无序列表"

        ```html
        <ul>
            <li>列表项 1</li>
            <li>列表项 2</li>
            <li>列表项 3</li>
        </ul>
        ```

    === "有序列表"

        ```html
        <ol>
            <li>列表项 1</li>
            <li>列表项 2</li>
            <li>列表项 3</li>
        </ol>
        ```

    === "嵌套列表"

        ```html
        <ul>
            <li>列表项 1
                <ol>
                    <li>子列表项 1</li>
                    <li>子列表项 2</li>
                </ol>
            </li>
            <li>列表项 2</li>
            <li>列表项 3</li>
        </ul>
        ```

    === "定义列表"

        ```html
        <dl>
            <dt>术语 1</dt>
            <dd>术语 1 的定义</dd>
            <dt>术语 2</dt>
            <dd>术语 2 的定义</dd>
        </dl>
        ```

### 链接

`<a>` 元素用来实现超链接的表示，它可以包裹任何内容，也自然会有是块级内容（比如标题、图片等）。

如上文所述，其基本格式为：`<a href="https://www.example.com">Content</a>`

- `title` 属性可以指定链接的补充信息，当我们将鼠标悬停在连接上时就会出现这个提示信息。
- `href` 的值可以是：
    - 网页地址（即 URL），有绝对地址和相对地址的表示法；
    - 电子邮件地址，以 `mailto:` 开头，后跟邮件地址；
    - 本地地址，建议使用相对地址：
        - 相对地址，当前页面的相对路径，比如说：`index.html`、`../index.html`、`./index.html`；
        - 要特别说明的是，`../` 表示上一级目录，`./` 表示当前目录；
    - 某个文档片段：`href` 的值以`#`开头，后面跟上文档片段（一个标题）的 `id`，这个 `id` 的值是该文档片段 `id` 属性的值

!!! example "链接"

    ```html
    <h2 id="Mailing_address">邮寄地址</h2>

    <p>请将您的申请邮寄到 <a href="#Mailing_address">邮寄地址</a>。</p>
    ```

    大家可以发现，我们目前的页面中，每一个小标题后面都有一个小锚点，这个小锚点就是我们在这里所说的 `id` 属性。

- `download` 属性：当链接是一个下载链接时，使用该属性可以指定下载文件被保存的文件名；

### 多媒体内容嵌入
#### 图片

`<img>` 元素用来在页面中嵌入图片。它是一个空元素，没有结束标签。它的基本格式为：`<img src="image.jpg" alt="描述信息">`。

- `src` 属性定义了图片的路径，可以是绝对路径或相对路径，也可以使用图像的绝对 URL 进行嵌入，建议使用相对路径；

> 未经许可，不要将 `src` 属性指向其他网站上的图像。这被称为“热链接（hotlinking）”。大多数人认为这是不道德的，因为这会导致每当有人访问你的页面，都会有另一些不知情的人为图像交付带宽费用。这也导致你无法掌控图像，图像有可能在你不知情的情况下，被删除或替换为尴尬的内容。

- `alt` 属性定义了图片的替代文本，当图片无法显示时，浏览器会显示这个文本。这个属性是必须的，应该简洁明了地描述图片的内容；

> 那么我们就可以发现，当我们使用 markdown 语法 `![alt text](image.jpg)` 来嵌入图片时，这里的 `alt text` 就是 `alt` 属性的值；`image.jpg` 就是 `src` 属性的值。

- `width` 和 `height` 属性定义了图片的宽度和高度，单位是像素（px），也可以使用百分比（%）；

> 如果没有指定图片的宽高，网页加载时若图片加载缓慢，图片下面的部分会先顶上来，等图片加载完后又会回去，比较影响阅读，因此建议设置这两个属性，网页就会预留出相对应的位置。

- `title` 属性定义了图片的补充信息，当我们将鼠标悬停在图片上时就会出现这个提示信息；

我们可以发现，对于 markdown 语法中的嵌入图片，我们是没有办法去设置图片的标题，或者说将解释说明与图片之间直接对应起来的，这个时候我们就需要使用 HTML 语法来嵌入图片了。

- `<figure>` 元素：用来定义图像、图表、照片等的容器，`<figure>` 标签将 `<img>` 和 `<figcaption>` 元素包裹起来；
- `<figcaption>` 元素：告诉浏览器和辅助技术工具，这段说明文字描述了 `<figure>` 元素的内容，通常放在 `<figure>` 元素的开头或结尾；

> figure 里不一定要是图片，可以是几张图片、一段代码、音视频、方程、表格或类似的东西。

我们可以给出一个在本文档中嵌入图片的例子：

```
<figure markdown="span">
    <center>
        ![Image title](./images/image-1)
    </center>
    <figcaption>Image caption</figcaption>
</figure>
```

或是彻底使用 HTML 语法来嵌入图片：

```html
<figure>
    <center>
        <img src="./images/image-1" alt="Image title">
    </center>
    <figcaption>Image caption</figcaption>
</figure>
```

??? tip "HTML 和 CSS 嵌入图片的区别"

    如果图像对你的内容有意义，则应使用 HTML 图像；如果图像纯粹是装饰，则应使用 CSS 背景图片。

    举一个 CSS 背景图片的例子：

    ```css
    p {
        background-image: url("images/dinosaur.jpg");
    }
    ```

    这段代码会在每个段落的背景中显示一张图片。我们可以使用 CSS 来控制图片的大小、位置等属性。

#### 矢量图

SVG（Scalable Vector Graphics）是一种基于 XML 的矢量图形格式，可以在网页中嵌入 SVG 图像。它的基本格式为：`<svg>` 元素。它们拥有较小的文件尺寸，却高度可缩放，所以它们不会在镜头拉近或者放大图像时像素化。

我们主要会遇见两种图形，位图和矢量图。

- 位图：位图使用像素网格来定义——位图文件精确包含每个像素的位置和它的色彩信息。流行的 web 位图格式包括 Bitmap（`.bmp`）、PNG（`.png`）、JPEG（`.jpg`），以及 GIF（`.gif`）；
- 矢量图：使用算法来定义——矢量图文件包含了图形和路径的定义，计算机可以根据这些定义计算出当它们在屏幕上渲染时应该呈现的样子。借助 SVG 格式，我们可以创造用于 Web 的精彩矢量图形。

位图的缺点是当我们放大图像时，图像会变得模糊，因为它们是由固定数量的像素组成的；而矢量图是由数学公式定义的图形，它们可以无限缩放而不会失去清晰度。

此外，矢量图像中的文本仍然可访问（这也有利于 SEO），SVG 还可以很好地适应样式/脚本，因为图像的每个组件都是可以通过 CSS 或通过 JavaScript 设置样式的元素。

我们可以用 HTML 来简单创建一些简单地 SVG 图形，但是对于复杂的图形，我们通常会使用一些图形编辑软件来创建 SVG 图像，然后将其嵌入到 HTML 中。并且我们有多种的嵌入方式，可以根据不同需求进行选择。

#### 音频和视频

视频元素 `<video>`，它可以嵌入内容（当视频无法加载出来时显示这段内容），它的属性有：

- `src`：视频路径，同图片元素；
- `controls`：（布尔属性），使视频播放具备基本的控制界面（比如暂停键、全屏键、音量调整等）；
- 可以为同一个视频配置多种播放源，从而提升视频的兼容性。这时不用 `src` 属性，而是在 `<video>` 标签内嵌入多个 `<source>` 空元素。这个元素包括 `src` 和 `type` 属性，前者同上，后者指明视频文件类型 `(video/xxx)`；

??? example "视频"

    ```html
    <video controls>
        <source src="movie.mp4" type="video/mp4">
        <source src="movie.webm" type="video/webm">
        <p>你的浏览器不支持此视频。可点击<a href="movie.mp4">此链接</a>观看</p>
    </video>
    ```

- `width` 和 `height` 属性定义了视频的宽度和高度，单位是像素（px），也可以使用百分比（%）；
- `poster` 属性定义了视频的预览图像，其值就是图片的路径；
- `muted` 属性定义了视频是否静音，布尔属性；
- `autoplay` 属性定义了视频是否自动播放，布尔属性；
- `loop` 属性定义了视频是否循环播放，布尔属性；
- `preload` 属性定义了视频的预加载方式，值可以是 `none`、`metadata` 和 `auto`；
    - `none`：不预加载视频；
    - `metadata`：只预加载视频的元数据（比如时长、尺寸等）；
    - `auto`：自动预加载视频；

音频元素 `<audio>`，与 `<video>` 元素类似，只有宽和高、`poster` 属性不存在，别的效果都与之类似。

此外，还可以引入字幕文件，在这里我们可以使用 `<track>` 元素来定义字幕文件，具体的细节就不予展开了。

### 表格

要完全用 HTML 来写表格不见得是很简单的一件事情，我们可以在了解表格大致的构造方法后，使用工具将一个可视化的表格转为 HTML 代码，然后我们可以再进行微调即可。

唯一需要提及一下的是：在 `<table>` 内嵌内容的开头使用 `<caption>` 元素，可以指定表格的标题。

### 表单

表单元素 `<form>` 用来定义一个表单，表单是用来收集用户输入的。具体内容较杂，边用边查即可。

### 页面整体架构

- `<header>`：定义了页面的头部，通常包含了网站的 logo、标题等；
- `<nav>`：定义了页面的导航栏，通常包含了子页面的标题等；
- `<main>`：定义了页面的主体，通常包含了页面的主要内容，只能有一个，且需要放在 `<body>` 内。主内容中还可以有各种子内容区段，可用 `<article>`、`<section>` 和 `<div>` 等元素表示；
    - `<article>`：定义了一个独立的内容区段，通常包含了一个完整的文章或博客；
    - `<section>`：定义了一个主题相关的内容区段，可以用于组织页面按其功能分块；
    - `<div>`：定义了一个通用的内容区段，通常用于布局或样式；
- `<aside>`：定义了页面的侧边栏，通常嵌套在 `<main>` 里；
- `<footer>`：定义了页面的底部，通常包含了网站的版权信息、联系信息等；

有了这些结构后，把布局交给 CSS 来处理就好了。

有时你会发现，对于一些要组织的项目或要包装的内容，现有的语义元素均不能很好对应。有时候你可能只想将一组元素作为一个单独的实体来修饰来响应单一的用 CSS 或 JavaScript。为了应对这种情况，HTML 提供了 `<div>` 和 `<span>` 元素。应配合使用 `class` 属性提供一些标签，使这些元素能易于查询。

`<span>` 是一个内联的（inline）无语义元素，可用于无法找到更好的语义元素来包含内容时，或者不想增加特定的含义时。例如：

```html
<p>这是一个 <span class="highlight">高亮</span> 的文本。</p>
```

`<div>` 是一个块级的（block）无语义元素，通常用于布局或样式。它可以包含其他块级元素和内联元素。例如：

```html
<div class="container">
    <h1>标题</h1>
    <p>这是一个段落。</p>
    <ul>
        <li>列表项 1</li>
        <li>列表项 2</li>
        <li>列表项 3</li>
    </ul>
</div>
```
在这里，我们使用了 `class` 属性来定义一个类名为 `container` 的样式，这个样式可以在 CSS 中定义。这样我们就可以通过 CSS 来控制这个元素的样式了。

此外，`<br>` 元素用来段落内换行，`<hr>` 元素用来做换行和水平分割线。

## 参考资料

- [MDN Web Docs - HTML](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Structuring_content)