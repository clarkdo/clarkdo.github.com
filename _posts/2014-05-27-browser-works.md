---
layout: post
title: 浏览器的渲染原理简介(转自酷壳)
comments: true
---
<link rel="stylesheet" href="http://yandex.st/highlightjs/8.0/styles/default.min.css">
<script src="http://yandex.st/highlightjs/8.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
<div class="post" id="post-9666">
    <h2>浏览器的渲染原理简介</h2>
    <div class="info">
        <span class="date">2013年5月22日</span>
        <span class="author"><a href="http://coolshell.cn/articles/author/haoel" title="由陈皓发布" rel="author">陈皓</a></span>                                       <span class="addcomment"><a href="#respond">发表评论</a></span>
        <span class="comments"><a href="#comments">阅读评论</a></span>
        <span class="comments"> 64,688 人阅读  &nbsp;  &nbsp;</span>
        <div class="fixed"></div>
    </div>
    <div class="content">
        <p>看到这个标题大家一定会想到这篇神文《<a href="http://taligarsiel.com/Projects/howbrowserswork1.htm" target="_blank">How Browsers Work</a>》，这篇文章把浏览器的很多细节讲得很细，而且也被<a href="http://ux.sohu.com/topics/50972d9ae7de3e752e0081ff" target="_blank">翻译成了中文</a>。为什么我还想写一篇呢？因为两个原因，</p>
        <p style="padding-left: 30px;">1）这篇文章太长了，阅读成本太大，不能一口气读完。</p>
        <p style="padding-left: 30px;">2）花了大力气读了这篇文章后可以了解很多，但似乎对工作没什么帮助。</p>
        <p>所以，我准备写下这篇文章来解决上述两个问题。希望你能在上班途中，或是坐马桶时就能读完，并能从中学会一些能用在工作上的东西。</p>
        <h4>浏览器工作大流程</h4>
        <p>废话少说，先来看个图：</p>
        <p style="text-align: center;"><img class="aligncenter  wp-image-9667" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/Render-Process.jpg" width="712" height="231"></p>
        <p style="text-align: left;">从上面这个图中，我们可以看到那么几个事：</p>
        <p><span id="more-9666"></span></p>
        <p style="text-align: left;">1）浏览器会解析三个东西：</p>
        <ul>
            <li>一个是HTML/SVG/XHTML，事实上，Webkit有三个C++的类对应这三类文档。解析这三种文件会产生一个DOM Tree。</li>
        </ul>
        <ul>
            <li>CSS，解析CSS会产生CSS规则树。</li>
        </ul>
        <ul>
            <li>Javascript，脚本，主要是通过DOM API和CSSOM API来操作DOM Tree和CSS Rule Tree.</li>
        </ul>
        <p>2）解析完成后，浏览器引擎会通过DOM Tree 和 CSS Rule Tree 来构造 Rendering Tree。注意：</p>
        <ul>
            <li>Rendering Tree 渲染树并不等同于DOM树，因为一些像Header或display:none的东西就没必要放在渲染树中了。</li>
        </ul>
        <ul>
            <li>CSS 的 Rule Tree主要是为了完成匹配并把CSS Rule附加上Rendering Tree上的每个Element。也就是DOM结点。也就是所谓的Frame。</li>
        </ul>
        <ul>
            <li>然后，计算每个Frame（也就是每个Element）的位置，这又叫layout和reflow过程。</li>
        </ul>
        <p>3）最后通过调用操作系统Native GUI的API绘制。</p>
        <h4>DOM解析</h4>
        <p>HTML的DOM Tree解析如下：</p>
        <pre>
            <code>&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;Web page parsing&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
    &lt;div&gt;
        &lt;h1&gt;Web page parsing&lt;/h1&gt;
        &lt;p&gt;This is an example Web page.&lt;/p&gt;
    &lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;</code>
        </pre>
        <p>上面这段HTML会解析成这样：</p>
        <p style="text-align: center;"><img class="aligncenter  wp-image-9669" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/DOM-Tree-01.jpg" width="456" height="300"></p>
        <p>下面是另一个有SVG标签的情况。</p>
        <p style="text-align: center;"><img class="aligncenter  wp-image-9670" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/DOM-Tree-02.jpg" width="408" height="320"></p>
        <h4>CSS解析</h4>
        <pre>
            <code>public class JdbcTemplate extends JdbcAccessor implements JdbcOperations {

    private static final String RETURN_RESULT_SET_PREFIX = "#result-set-";

    private static final String RETURN_UPDATE_COUNT_PREFIX = "#update-count-";

}</code>
        </pre>
        <p>CSS的解析大概是下面这个样子（下面主要说的是Gecko也就是Firefox的玩法），假设我们有下面的HTML文档：</p>

        <pre>
            <code>&lt;doc&gt;
    &lt;title&gt;A few quotes&lt;/title&gt;
    &lt;para&gt;
      Franklin said that &lt;quote&gt;"A penny saved is a penny earned."&lt;/quote&gt;
    &lt;/para&gt;
    &lt;para&gt;
      FDR said &lt;quote&gt;"We have nothing to fear but &lt;span&gt;fear itself.&lt;/span&gt;"&lt;/quote&gt;
    &lt;/para&gt;
&lt;/doc&gt;</code>
        </pre>
  <p>于是DOM Tree是这个样子：</p>
  <p><img class="aligncenter size-full wp-image-9672" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/DOM-Tree-Example.jpg" width="368" height="318"></p>
  <p>然后我们的CSS文档是这样的：</p>
  <div><div id="highlighter_265734" class="syntaxhighlighter coolshell_syntaxhighlighter xml"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="xml spaces">&nbsp;</code><code class="xml plain">/* rule 1 */ doc { display: block; text-indent: 1em; }</code></div><div class="line number2 index1 alt1"><code class="xml plain">/* rule 2 */ title { display: block; font-size: 3em; }</code></div><div class="line number3 index2 alt2"><code class="xml plain">/* rule 3 */ para { display: block; }</code></div><div class="line number4 index3 alt1"><code class="xml plain">/* rule 4 */ [class="emph"] { font-style: italic; }</code></div></div></td></tr></tbody></table></div></div>
  <p>于是我们的CSS Rule Tree会是这个样子：</p>
  <p><img class="aligncenter size-full wp-image-9673" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/CSS-Rule-Tree-Example.jpg" width="397" height="238"></p>
  <p>注意，图中的第4条规则出现了两次，一次是独立的，一次是在规则3的子结点。所以，我们可以知道，建立CSS Rule Tree是需要比照着DOM Tree来的。CSS匹配DOM Tree主要是从右到左解析CSS的Selector，好多人以为这个事会比较快，其实并不一定。关键还看我们的CSS的Selector怎么写了。</p>
  <p><strong>注意：CSS匹配HTML元素是一个相当复杂和有性能问题的事情。所以，你就会在N多地方看到很多人都告诉你，DOM树要小，CSS尽量用id和class，千万不要过渡层叠下去，……</strong></p>
  <p>通过这两个树，我们可以得到一个叫Style Context Tree，也就是下面这样（把CSS Rule结点Attach到DOM Tree上）：</p>
  <p><img class="aligncenter size-full wp-image-9674" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/CSS-Content-Tree-Example.jpg" width="405" height="318"></p>
  <p>所以，Firefox基本上来说是通过CSS 解析 生成 CSS Rule Tree，然后，通过比对DOM生成Style Context Tree，然后Firefox通过把Style Context Tree和其Render Tree（Frame Tree）关联上，就完成了。注意：Render Tree会把一些不可见的结点去除掉。而<strong>Firefox中所谓的Frame就是一个DOM结点，不要被其名字所迷惑了</strong>。</p>
  <p style="text-align: center;"><img class="aligncenter  wp-image-9677" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/Firefox-style-context-tree.png" width="328" height="366"></p>
  <p>注：Webkit不像Firefox要用两个树来干这个，Webkit也有Style对象，它直接把这个Style对象存在了相应的DOM结点上了。</p>
  <h4>渲染</h4>
  <p>渲染的流程基本上如下（黄色的四个步骤）：</p>
  <ol>
    <li>计算CSS样式</li>
    <li>构建Render Tree</li>
    <li>Layout – 定位坐标和大小，是否换行，各种position, overflow, z-index属性 ……</li>
    <li>正式开画</li>
</ol>
<p style="text-align: center;"><img class="aligncenter  wp-image-9675" alt="" src="http://coolshell.cn//wp-content/uploads/2013/05/Render-Process-Skipping.jpg" width="712" height="196"></p>
<p style="text-align: left;">注意：上图流程中有很多连接线，这表示了Javascript动态修改了DOM属性或是CSS属会导致重新Layout，有些改变不会，就是那些指到天上的箭头，比如，修改后的CSS rule没有被匹配到，等。</p>
<p style="text-align: left;">这里重要要说两个概念，一个是Reflow，另一个是Repaint。这两个不是一回事。</p>
<ul>
    <li>Repaint——屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变。</li>
</ul>
<ul>
    <li>Reflow——意味着元件的几何尺寸变了，我们需要重新验证并计算Render Tree。是Render Tree的一部分或全部发生了变化。这就是Reflow，或是Layout。（<strong>HTML使用的是flow based layout，也就是流式布局，所以，如果某元件的几何尺寸发生了变化，需要重新布局，也就叫reflow</strong>）reflow 会从&lt;html&gt;这个root frame开始递归往下，依次计算所有的结点几何尺寸和位置，在reflow过程中，可能会增加一些frame，比如一个文本字符串必需被包装起来。</li>
</ul>
<p>下面是一个打开Wikipedia时的Layout/reflow的视频（注：HTML在初始化的时候也会做一次reflow，叫 <dfn>intial reflow</dfn>），你可以感受一下：</p>
<p></p><center><object width="480" height="400" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" align="middle"><param name="src" value="http://opengg.spring-world.net/flashes/youku/loader.swf?VideoIDS=XMzI5MDg0OTA0&amp;embedid=MTI0LjkzLjE5Ny4xMgI4MjI3MTIyNgJjb29sc2hlbGwuY24CL2FydGljbGVzLzk2NjYuaHRtbA%3D%3D&amp;wd=&amp;vext=pid%3D%26emb%3DMTI0LjkzLjE5Ny4xMgI4MjI3MTIyNgJjb29sc2hlbGwuY24CL2FydGljbGVzLzk2NjYuaHRtbA%3D%3D%26bc%3D%26type%3D0"><param name="allowfullscreen" value="true"><param name="quality" value="high"><param name="allowscriptaccess" value="always"><embed width="480" height="400" type="application/x-shockwave-flash" src="http://opengg.spring-world.net/youkuyidiantong/player.swf?showAd=0&amp;VideoIDS=XMzI5MDg0OTA0" allowfullscreen="true" quality="high" allowscriptaccess="always" align="middle"></object></center>Reflow的成本比Repaint的成本高得多的多。DOM Tree里的每个结点都会有reflow方法，一个结点的reflow很有可能导致子结点，甚至父点以及同级结点的reflow。<strong>在一些高性能的电脑上也许还没什么，但是如果reflow发生在手机上，那么这个过程是非常痛苦和耗电的</strong>。<p></p>
<p>所以，下面这些动作有很大可能会是成本比较高的。</p>
<ul>
    <li>当你增加、删除、修改DOM结点时，会导致Reflow或Repaint</li>
    <li>当你移动DOM的位置，或是搞个动画的时候。</li>
    <li>当你修改CSS样式的时候。</li>
    <li>当你Resize窗口的时候（移动端没有这个问题），或是滚动的时候。</li>
    <li>当你修改网页的默认字体时。</li>
</ul>
<p style="text-align: left;">注：display:none会触发reflow，而visibility:hidden只会触发repaint，因为没有发现位置变化。</p>
<p style="text-align: left;">多说两句关于滚屏的事，通常来说，如果在滚屏的时候，我们的页面上的所有的像素都会跟着滚动，那么性能上没什么问题，因为我们的显卡对于这种把全屏像素往上往下移的算法是很快。但是如果你有一个fixed的背景图，或是有些Element不跟着滚动，有些Elment是动画，那么这个滚动的动作对于浏览器来说会是相当相当痛苦的一个过程。你可以看到很多这样的网页在滚动的时候性能有多差。因为滚屏也有可能会造成reflow。</p>
<p style="text-align: left;">基本上来说，reflow有如下的几个原因：</p>
<ul>
    <li>Initial。网页初始化的时候。</li>
    <li>Incremental。一些Javascript在操作DOM Tree时。</li>
    <li>Resize。其些元件的尺寸变了。</li>
    <li>StyleChange。如果CSS的属性发生变化了。</li>
    <li>Dirty。几个Incremental的reflow发生在同一个frame的子树上。</li>
</ul>
<p style="text-align: left;">好了，我们来看一个示例吧：</p>
<div><div id="highlighter_767764" class="syntaxhighlighter coolshell_syntaxhighlighter jscript"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div><div class="line number12 index11 alt1">12</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="jscript keyword">var</code> <code class="jscript plain">bstyle = document.body.style; </code><code class="jscript comments">// cache</code></div><div class="line number2 index1 alt1">&nbsp;</div><div class="line number3 index2 alt2"><code class="jscript plain">bstyle.padding = </code><code class="jscript string">"20px"</code><code class="jscript plain">; </code><code class="jscript comments">// reflow, repaint</code></div><div class="line number4 index3 alt1"><code class="jscript plain">bstyle.border = </code><code class="jscript string">"10px solid red"</code><code class="jscript plain">; </code><code class="jscript comments">//&nbsp; 再一次的 reflow 和 repaint</code></div><div class="line number5 index4 alt2">&nbsp;</div><div class="line number6 index5 alt1"><code class="jscript plain">bstyle.color = </code><code class="jscript string">"blue"</code><code class="jscript plain">; </code><code class="jscript comments">// repaint</code></div><div class="line number7 index6 alt2"><code class="jscript plain">bstyle.backgroundColor = </code><code class="jscript string">"#fad"</code><code class="jscript plain">; </code><code class="jscript comments">// repaint</code></div><div class="line number8 index7 alt1">&nbsp;</div><div class="line number9 index8 alt2"><code class="jscript plain">bstyle.fontSize = </code><code class="jscript string">"2em"</code><code class="jscript plain">; </code><code class="jscript comments">// reflow, repaint</code></div><div class="line number10 index9 alt1">&nbsp;</div><div class="line number11 index10 alt2"><code class="jscript comments">// new DOM element - reflow, repaint</code></div><div class="line number12 index11 alt1"><code class="jscript plain">document.body.appendChild(document.createTextNode(</code><code class="jscript string">'dude!'</code><code class="jscript plain">));</code></div></div></td></tr></tbody></table></div></div>
<p style="text-align: left;">当然，我们的浏览器是聪明的，它不会像上面那样，你每改一次样式，它就reflow或repaint一次。<strong>一般来说，浏览器会把这样的操作积攒一批，然后做一次reflow，这又叫异步reflow或增量异步reflow</strong>。但是有些情况浏览器是不会这么做的，比如：resize窗口，改变了页面默认的字体，等。对于这些操作，浏览器会马上进行reflow。</p>
<p style="text-align: left;">但是有些时候，我们的脚本会阻止浏览器这么干，比如：如果我们请求下面的一些DOM值：</p>
<ol>
    <li>offsetTop, offsetLeft, offsetWidth, offsetHeight</li>
    <li>scrollTop/Left/Width/Height</li>
    <li>clientTop/Left/Width/Height</li>
    <li>IE中的 getComputedStyle(), 或 currentStyle</li>
</ol>
<p style="text-align: left;">因为，如果我们的程序需要这些值，那么浏览器需要返回最新的值，而这样一样会flush出去一些样式的改变，从而造成频繁的reflow/repaint。</p>
<h4 style="text-align: left;">减少reflow/repaint</h4>
<p>下面是一些Best Practices：</p>
<p><strong>1）不要一条一条地修改DOM的样式。与其这样，还不如预先定义好css的class，然后修改DOM的className。</strong></p>
<div><div id="highlighter_375051" class="syntaxhighlighter coolshell_syntaxhighlighter jscript"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="jscript comments">// bad</code></div><div class="line number2 index1 alt1"><code class="jscript keyword">var</code> <code class="jscript plain">left = 10,</code></div><div class="line number3 index2 alt2"><code class="jscript plain">top = 10;</code></div><div class="line number4 index3 alt1"><code class="jscript plain">el.style.left = left + </code><code class="jscript string">"px"</code><code class="jscript plain">;</code></div><div class="line number5 index4 alt2"><code class="jscript plain">el.style.top&nbsp; = top&nbsp; + </code><code class="jscript string">"px"</code><code class="jscript plain">;</code></div><div class="line number6 index5 alt1">&nbsp;</div><div class="line number7 index6 alt2"><code class="jscript comments">// Good</code></div><div class="line number8 index7 alt1"><code class="jscript plain">el.className += </code><code class="jscript string">" theclassname"</code><code class="jscript plain">;</code></div><div class="line number9 index8 alt2">&nbsp;</div><div class="line number10 index9 alt1"><code class="jscript comments">// Good</code></div><div class="line number11 index10 alt2"><code class="jscript plain">el.style.cssText += </code><code class="jscript string">"; left: "</code> <code class="jscript plain">+ left + </code><code class="jscript string">"px; top: "</code> <code class="jscript plain">+ top + </code><code class="jscript string">"px;"</code><code class="jscript plain">;</code></div></div></td></tr></tbody></table></div></div>
<p><strong>2）把DOM离线后修改。如：</strong></p>
<ul>
    <li>使用documentFragment 对象在内存里操作DOM</li>
    <li>先把DOM给display:none(有一次reflow)，然后你想怎么改就怎么改。比如修改100次，然后再把他显示出来。</li>
    <li>clone一个DOM结点到内存里，然后想怎么改就怎么改，改完后，和在线的那个的交换一下。</li>
</ul>
<p>3）<strong>不要把DOM结点的属性值放在一个循环里当成循环里的变量。</strong>不然这会导致大量地读写这个结点的属性。</p>
<p>4）<strong>尽可能的修改层级比较低的DOM</strong>。当然，改变层级比较底的DOM有可能会造成大面积的reflow，但是也可能影响范围很小。</p>
<p>5）<strong>为动画的HTML元件使用fixed或absoult的position</strong>，那么修改他们的CSS是不会reflow的。</p>
<p>6）<strong>千万不要使用table布局</strong>。因为可能很小的一个小改动会造成整个table的重新布局。</p>
<blockquote><p>In this manner, the user agent can begin to lay out the table once the entire first row has been received. Cells in subsequent rows do not affect column widths. Any cell that has content that overflows uses the ‘overflow’ property to determine whether to clip the overflow content.</p>
    <p><cite><a href="http://www.w3.org/TR/CSS21/tables.html#fixed-table-layout">Fixed layout, CSS 2.1 Specification</a></cite></p></blockquote>
    <blockquote><p>This algorithm may be inefficient since it requires the user agent to have access to all the content in the table before determining the final layout and may demand more than one pass.</p>
        <p><cite><a href="http://www.w3.org/TR/CSS21/tables.html#auto-table-layout">Automatic layout, CSS 2.1 Specification</a></cite></p></blockquote>
        <h4>几个工具和几篇文章</h4>
        <p>有时候，你会也许会发现在IE下，你不知道你修改了什么东西，结果CPU一下子就上去了到100%，然后过了好几秒钟repaint/reflow才完成，这种事情以IE的年代时经常发生。所以，我们需要一些工具帮我们看看我们的代码里有没有什么不合适的东西。</p>
        <ul>
            <li>Chrome下，Google的<a href="http://code.google.com/webtoolkit/speedtracer/">SpeedTracer</a>是个非常强悍的工作让你看看你的浏览渲染的成本有多大。其实Safari和Chrome都可以使用开发者工具里的一个Timeline的东东。</li>
        </ul>
        <ul>
            <li>Firefox下这个基于Firebug的叫<a href="https://addons.mozilla.org/en-US/firefox/addon/firebug-paint-events/" target="_blank">Firebug Paint Events</a>的插件也不错。</li>
        </ul>
        <ul>
            <li>IE下你可以用一个叫<a href="http://ajax.dynatrace.com/pages/">dynaTrace</a>的IE扩展。</li>
        </ul>
        <p>最后，别忘了下面这几篇提高浏览器性能的文章：</p>
        <ul>
            <li><a href="http://code.google.com/speed/page-speed/docs/rules_intro.html">Google – Web Performance Best Practices</a></li>
            <li><a href="http://developer.yahoo.com/performance/rules.html">Yahoo – Best Practices for Speeding Up Your Web Site</a></li>
            <li><a href="http://stevesouders.com/hpws/rules.php">Steve Souders – 14 Rules for Faster-Loading Web Sites</a></li>
        </ul>
        <h4>参考</h4>
        <ul>
            <li>David Baron的演讲：Fast CSS: How Browsers Lay Out Web Pages：<a href="http://dbaron.org/talks/2012-03-11-sxsw/slide-1.xhtml" target="_blank">slideshow</a>, <a href="http://dbaron.org/talks/2012-03-11-sxsw/master.xhtml">all slides</a>, <a href="http://audio.sxsw.com/2012/podcasts/11-ACC-Fast_CSS_How_Browser_Layout.mp3">audio (MP3)</a>, <a href="http://schedule.sxsw.com/2012/events/event_IAP12909">Session page</a>, <a href="http://lanyrd.com/2012/sxsw-interactive/spmbt/">Lanyrd page</a></li>
        </ul>
        <ul>
            <li>How Browsers Work: <a href="http://taligarsiel.com/Projects/howbrowserswork1.htm" target="_blank">http://taligarsiel.com/Projects/howbrowserswork1.htm</a></li>
        </ul>
        <ul>
            <li>Mozilla 的 Style System Overview：<a href="https://developer.mozilla.org/en-US/docs/Style_System_Overview" target="_blank">https://developer.mozilla.org/en-US/docs/Style_System_Overview</a></li>
        </ul>
        <ul>
            <li>Mozilla 的 Note of reflow： <a href="http://www-archive.mozilla.org/newlayout/doc/reflow.html" target="_blank">http://www-archive.mozilla.org/newlayout/doc/reflow.html</a></li>
        </ul>
        <ul>
            <li>Rendering: repaint, reflow/relayout, restyle：<a href="http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/" target="_blank">http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/</a></li>
        </ul>
        <ul>
            <li>Effective Rendering CSS：<a href="http://css-tricks.com/efficiently-rendering-css/" target="_blank">http://css-tricks.com/efficiently-rendering-css/</a></li>
        </ul>
        <ul>
            <li><strong></strong>Webkit Rendering文档：<a href="http://trac.webkit.org/wiki/WebCoreRendering" target="_blank">http://trac.webkit.org/wiki/WebCoreRendering</a></li>
        </ul>
        <div style="margin-top: 15px; font-size: 11px;color: #cc0000;">
            <p align="center"><strong>（转载本站文章请注明作者和出处 <a href="http://coolshell.cn/">酷 壳 – CoolShell.cn</a> ，请勿用于任何商业用途）</strong></p></div>
            <div style="text-align:center;padding:0px;font-size: 14px;margin-bottom: 50px;">——=== <b>访问 <a href="http://coolshell.cn/404/" target="_blank">酷壳404页面</a> 寻找遗失儿童。</b> ===——</div>
            <div style="clear:both; margin-top:5px; margin-bottom:5px;"></div><div style="clear:both; margin-top:5px; margin-bottom:5px;"></div>         <div class="fixed"></div>
        </div>
    </div>