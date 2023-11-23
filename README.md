# 说明
python脚本下载[jyy老师](https://jyywiki.cn/index.html)的主页课程课件。

如有文件缺失或者代码bug，欢迎补充。

# 使用和说明

运行脚本后输入对应选项即可选择要下载的课件和相关资源，按下回车默认下载2023年课件和相关的资源。

所有的课件内容都在与脚本的同路径下的文件夹`Courseware`中。

当注释下面这行时，再执行代码无论下载哪个年份的代码时，你会下载到`https://jyywiki.cn/`下**几乎**所有的文件。
```python
#"https://jyywiki.cn/index.html",                           # unnecessary
```
原因是文件中存在相互引用，而`https://jyywiki.cn/index.html`中包含所有的课程链接，而课程文件中又包含了主界面和其他资源文件的链接……

至于为什么是**几乎**，是因为还有一些文件的链接并不在`https://jyywiki.cn/`下的任何一个文件中，文件中没有给出链接，那也就意味着得不到文件地址，也就下载不到该文件。

由于文件过多，可能会占用比较多的磁盘空间(150MB~200MB【随时间变化】），你也可以选择在下载下来后删除不必要的文件，仅仅保留你想要的文件。

还有部分文件内嵌在`ipynb.html`中，没有指明文件链接，同时，由于生成`html`文件压缩了代码，嵌入在`html`中的文件也是不可读的，所以当`ipynb.html`代码无法显示时，只能在往期的版本中查找是否有对应的`html`课件。如果你愿意你可以提交issue，我会尽力更新代码及文件资源。


# 更新
## V2.5 2023-11-23
很久没有维护仓库了，其实也知道存在一些问题。今天一并完善好了。

本次更新修复了随着jyy老师更新课件而产生的bug，主要的问题是新的课件中路径的转变，导致以前的代码运行会出现众多的文件链接生成错误。在Linux下执行脚本也会出现这样链接的问题，原因是不同操作系统使用的分隔符不同。

- 重写了路径生成的相关代码，但这部分代码有点难以阅读，也没有写注释，总体还是在解决路径问题，包括Linux和Windows下不同的分隔符。
- 添加了`visualize.py`这个文件到`Courseware/pages/OS/2022/demos/visualize.py`路径下，该文件属于文件中没有引用链接仅有关键字的文件，而且在`https://jyywiki.cn/`网站中也没有这个文件。
- 发现本地的文件没办法加载出PPT，原因出在了`src="/OS/2023/build/slides/xx.xx.slides.html"`，对比以前的文件发现是这么写的`../slides/xx.xx.slides.html`，能够正常加载PPT，添加了一个`file_fix()`，将`/OS/build/2023/slides`替换为`../sildes`。【防止出现误替换，所以加长了匹配的字符】
- 修复了`file_fix()`没有判定文件夹存在可能导致错误的问题。

## V2.4 2023-09-14
忘了验证其他年份课件的下载了，然后发现jupyter课件中的`href`属性中提供的链接是这样的
```html
<h3 id="Demo%EF%BC%9A%E4%BD%BF%E7%94%A8-tar-%E5%91%BD%E4%BB%A4">Demo&#65306;&#20351;&#29992; tar &#21629;&#20196;<a class="anchor-link" href="lect1.ipynb.html#Demo%EF%BC%9A%E4%BD%BF%E7%94%A8-tar-%E5%91%BD%E4%BB%A4">&#182;</a>
```
导致解析后下载出现了奇怪的文件夹，添加一行`link = urlparse(link).path`即可解决问题，这行代码直接提取出目的相对链接`lect1.ipynb.html`

## V2.3 2023-09-14
算是比较重要的一次更新了，本次更新的代码解决了课件中文件下载不全的问题，之前下载不全是因为要猜测会有什么样类型的文件，现在不用猜测了，只需要跟着链接下载即可。

问题也随之而来，老师的`html`文件中出现了莫名其妙的路径引用比如
```html
<p class="font-serif my-1"><object alt="" class="center" src="/pages/OS/2021/labs/../../img/flame-graph.svg" width="500px" data="../../../pages/OS/img/flame-graph.svg"></object></p>
```
首先这个办法是没有问题的，确实识别到了众多文件类型，`cc`,`go`,`svg`,`pdf`等等。

但是这个问题出现的时候直接导致代码出现了`IndexError`，查了原因就是`/pages/OS/2021/labs/../../img/flame-graph.svg`惹的祸，然后识别出现问题，可我又要下载怎么办呢？难不成要进行特判？我不能接受，然后看到了后面的`data`，然后就通过`data`来下载这个文件了。~~速度我管不着，代码简洁才是真。~~


## V1.0 2023-09-07
第一版代码写得略微粗糙，全是for+if，逻辑很朴实，大致思路就是从index.html开始下载，然后对下载的文件中的资源执行分析，然后对分析的链接再次下载......反复执行这个过程。
主要的限制在于只能下载2023年的课件。
