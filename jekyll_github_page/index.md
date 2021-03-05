# Jekyll + GitHub Pages 搭建博客流程


## 使用 github pages

首先在 github 中建立一个基于自己用户名的 repository：比如我建立一个名为 [bqwhnn.github.io][bqwhnn.github.io] 的 repo。

系统在检测到这样格式的 repo 文件名后，会在 setting 中自动开启 GitHub Pages 功能。之后可以将这个 repo clone 到本地进行修改，在里面上传的网页就是你的网站的内容。

## 部署 [jekyll][jekyll]

### 前置：[Ruby][Ruby]

Mac 和 Linux 可以用 Terminal 配合 [yum][yum] 或者 [brew][brew] 这样的包管理器来安装 ruby，Windows 下可以更方便地使用集成好的 [RubyInstaller][RubyInstaller] 进行安装。

### [RubyGems][RubyGems]

gem 是一个 ruby 的包管理系统，使用 gem 可以方便的在本地安装 ruby 应用。

安装方法
> // 在 RubyGems 官网下载压缩包，解压到本地任意位置  
> // 在 Terminal 中输入  
> $ cd your path to RubyGems  // 解压位置  
> $ ruby setup.rb

### 安装 jekyll

类似 nodejs 和 npm 的包安装，使用 gem 安装 jekyll，直接在 Terminal 中输入：

> $ gem install bundler // 先安装依赖包 bundler  
> $ gem install jekyll

jekyll 模板网站：[Jekyll Themes][Jekyll Themes]

使用模板可能会缺少东西，看错误提示缺什么直接
> $ gem install XXXX

### 网站预览

> $ cd your website path  
> $ jekyll serve  
> // 一个开发服务器将会运行在 http://localhost:4000/  
> // 在浏览器输入网址就可以在本地服务器看到网站

## 参考

* [github上利用jekyll搭建自己的blog的操作顺序？](https://www.zhihu.com/question/30018945?sort=created)
* [jekyll博客搭建之艰辛之路](http://www.jianshu.com/p/27de87d4447e)


[bqwhnn.github.io]: https://bqwhnn.github.io
[jekyll]: http://jekyll.com.cn/
[Ruby]: https://www.ruby-lang.org/en/
[yum]: http://yum.baseurl.org/
[brew]: https://brew.sh/
[RubyInstaller]: https://rubyinstaller.org/
[RubyGems]: https://rubygems.org/pages/download
[Jekyll Themes]: http://jekyllthemes.org/

