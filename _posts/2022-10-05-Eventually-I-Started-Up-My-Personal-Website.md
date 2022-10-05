---
title: 网站搭建过程记载
date: 2022-10-05 21:18:00 +0800
categories: [Blogging, Tutorial]
tags: [Blog]
render_with_liquid: false

---

第一篇打算记录一下搭网站的心路历程，再详细写一写搭建方法。



# 1. 为啥要费功夫来搭这个网站

> 能让我着迷并着手去做的事情，一般都是有挑战并足够小众的事情。如果这个事儿我身边有太多人正在做，而且轻而易举就能完成，那就算我很喜欢这件事情本身，但我大概率也不会去做了。但如果这件事情对我来说是必要的，我可能会另辟蹊径去做。这就是我的做事逻辑。

—— 好像在说绕口令一样

就事论事的话，在决定搭个人网站之前，我的需求是：

1. ”写一下刷题心得以便面试前抱佛脚”、“记录一下看到的编曲的tips免得自己太久不看而忘记了”、以及“很偶尔很偶尔的情况下写一写我对于某些事的某些看法”;
2. 最好我还能把这些东西便利地分享给朋友（如果他们想看的话）;
3. 最好最好这个界面还足够漂亮，搜索还足够方便.

那其实，如果只需要做笔记的话，像Typora、Notion这种Markdown软件已经够用了。但这两个软件还是局限于个人笔记和团队开发，随意分享功能做的不是很到位。

如果说要加上分享功能的话，整一个公众号倒是不错的选择。但我票圈好多人都有公众号，以至于这件事在我心里变得太大众和一点都不酷。

我也想过直接在CSDN或者什么博客园整一个博客，但界面实在太平凡：大家都用一样的背景、一样的字体、一样的主页排版方式。千篇一律，就是对我的一个直接劝退。

思来想去，感觉还是建一个个人网站比较好。界面也可以自己选，排版也不用拘泥在模板里，搜索功能杠杠的，分享还方便。

**所以就开始干咯。**

另外插一句话，我还是不太敢把自己那些“第二天就会觉得自己很傻逼”的思想发在网络上，所以这个网站可能也不会分享给太多的人。

如果你能看到这些话，那么要么是我敞开了心扉不再顾虑自己贫瘠的思维暴露在大众视野里可能会对我个人形象带来不好的影响这件事，要么你就是我很好很信任的人。

不过，我还是我希望能慢慢适应分享这件事本身。

说到这里，推荐一本书： [*Show Your Work*](https://www.amazon.com/Show-Your-Work-Austin-Kleon/dp/076117897X) (by Austin Kleon)。这本书让我认识到“分享过程”的意义。

<img src="https://m.media-amazon.com/images/P/076117897X.01._SCLZZZZZZZ_SX500_.jpg" alt="Show your work" style="zoom:50%;" />

<center>The Cover Of Show Your Work</center>

也就是说，相对于作品本身，人们更好奇的是你创作的过程，而这个过程也许能帮助到很多人。

比如说，

- 鼠拨土斯基(Schubotsky)今天进行了一个音的混，拿去给朋友听。
- 朋友稍微也懂点行，说：很好呀你这混的！诶你能不能告诉我这个地方的人声怎么变丰满的？
- 鼠拨土斯基说：噢！这个地方，人声叠了五轨，主Vocal一轨，高低八度两轨，走和弦音高低各一轨。
- 朋友说：噢！原来如此！我一直都在想这个问题，还有很多朋友跟我有一样的疑惑。你这真是一语点醒梦中人！你可以把这个做个对比视频分享一下吗？这样能让更多的人能看到，解决他们的问题。

对鼠拨土斯基来说，这可能只是一件稀松平常的事情。但别人眼里，这是一个黑匣子。在鼠拨土斯基分享这件事的时候，就好像展示了一场魔术的揭秘。这是很酷的事情，且有意义。

受到这个启发，我开始想把自己学到的东西放在一个大家都能看到的平台上，让我的内容成为一些问题潜在的且可寻得的答案。



# 2. 魔术揭秘

我之前也觉得搭建一个个人网站是很困难的事情，现在弄好了，就不这么觉得了。

不过在这个过程中也有遇到一些实在的阻力，会在下面详细说说的。

## (i) Meet Jekyll

[Jekyll](https://jekyllrb.com/)  是一个把markdown文本转换静态网站/博客（其实就是html）的工具。Github上有不少好的Jekyll主题，稍微改改内容就能直接用，而且还能借助Github来host这个网站。

不买域名的话，这个过程不需要花一分钱。

- 这一步，其实就只需要在终端输入下面这句话：

  ```console
  gem install bundler jekyll
  ```

- 虽然但是，这第一步我当时就遇到了问题。我的mac疯狂连环报错，Stack Overflow也解决不了问题。但还好我发现了这个[网站](https://www.moncefbelyamani.com/the-definitive-guide-to-installing-ruby-gems-on-a-mac/)，如果你也是MacOS，跟着这个一步步做就可以成功install。大概步骤就是：

  - 先安装一个Homebrew，我建议用homebrew[官网](https://brew.sh/)的指令下载

    ```console
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

  - 借助homebrew来安装ruby

    ```console
    brew install ruby
    ```

  - 把环境变量更新到homebrew安装的Ruby的路径下

    ```console
    echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc
    ```

  - 重启你的终端（这一步很重要！），再确认一下你安装的ruby路径对不对

    ```console
    which ruby
    ```

    如果没问题的话，你会得到`/usr/local/opt/ruby/bin/ruby`或者`/opt/homebrew/opt/ruby/bin`

  - 再输入一开始的指令

    ```console
    gem install jekyll
    ```

应该是这么个过程，具体的细节我也忘记了。但按照那个网站里的步骤来做肯定没错。



## (ii) Choose a theme

Jekyll的[主题官网](https://jekyllthemes.io/)上有很多漂亮的模板，可以选一个你心仪的。

我用的是**Jekyll Chirpy Theme**，具体过程如下：

1. 点进这个Github[链接](https://github.com/cotes2020/jekyll-theme-chirpy)，fork一下这个代码，名字得是 <u>xxx.github.io</u>，选择对外开放public.

2. 把这个代码下载到本地仓库

   ```console
   cd 你想存放代码的路径
   git init
   git remote add origin 你fork仓库的http链接
   git pull
   git checkout master
   ```



## 3. Change the defaults

打开下载的代码，修改里面的一些默认设置。

- 主要是需要修改 `_config.yml`里面的一些信息
- 如果想要发布一篇新博客，在 `_posts`里面创建一个.md文件，且格式得为`yyyy-mm-dd-your-title`

- 如果想要在本地查看效果，在终端输入：

  ```console
  bundle exec jekyll s
  ```

 

## 4. Host on Github

1. 把本地的更新同步到Github仓库，在终端输入：

   ```console
   git status
   git add .
   git commit -m "your change"
   git pull
   ```

2. 打开 Github repository，点击页面上方的 "Actions"

   - 你会看到正在运行的workflows
   - 等待运行完毕并出现绿色的勾

3. 点击页面上方的 "Settings"

   - 在页面左边出现的栏目中选择 "Pages"
   - 把 "Source" 修改为 Github Actions

4. 打开 <u>你的名字.github.io</u>，你会惊喜地发现网站已经创建完毕。



到此为止，这就是搭建网站的全过程。

















