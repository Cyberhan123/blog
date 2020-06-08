# Cyberhan 的博客

### netlify 集成自动化（提交即可触发发布）

[![Netlify Status](https://api.netlify.com/api/v1/badges/0cb187d0-646f-455d-9102-314e25512cd3/deploy-status)](https://app.netlify.com/sites/cyberhan123/deploys)

### 将我的项目部署到你的netlify上


点击下方按钮：克隆本项目并部署到Netlify


<a href="https://app.netlify.com/start/deploy?repository=https://github.com/Cyberhan123/blog"><img src="https://www.netlify.com/img/deploy/button.svg" alt="克隆本项目并部署到Netlify"></a>



### 同步语雀项目

-  设置语雀token


    这个秘钥只是可读，我就暴露在package.json了省的自己之后设置麻烦

   
-  同步语雀命令

    bash 运行
    `npm run sync`




### 主题使用

-  ocean配置注意

    设置themes内的_config.yml文件


#  下面是使用的插件的教程



## [yuque-hexo](https://github.com/x-cold/yuque-hexo#Example)

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]
[![Known Vulnerabilities][snyk-image]][snyk-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/yuque-hexo.svg?style=flat-square
[npm-url]: https://npmjs.org/package/yuque-hexo
[travis-image]: https://img.shields.io/travis/x-cold/yuque-hexo.svg?style=flat-square
[travis-url]: https://travis-ci.org/x-cold/yuque-hexo
[codecov-image]: https://codecov.io/gh/x-cold/yuque-hexo/branch/master/graph/badge.svg
[codecov-url]: https://codecov.io/gh/x-cold/yuque-hexo
[david-image]: https://img.shields.io/david/x-cold/yuque-hexo.svg?style=flat-square
[david-url]: https://david-dm.org/x-cold/yuque-hexo
[snyk-image]: https://snyk.io/test/npm/yuque-hexo/badge.svg?style=flat-square
[snyk-url]: https://snyk.io/test/npm/yuque-hexo
[download-image]: https://badgen.net/npm/dt/yuque-hexo
[download-url]: https://npmjs.org/package/yuque-hexo

A downloader for articles from yuque（语雀知识库同步工具）

### Usage

#### Premise

事先拥有一个 [hexo](https://github.com/hexojs/hexo) 项目，并在 `package.json` 中配置相关信息，可参考 [例子](#Example)。

### Config

#### 配置 TOKEN

出于对知识库安全性的调整，使用第三方 API 访问知识库，需要传入环境变量 YUQUE_TOKEN，在语雀上点击 个人头像 -> 设置 -> Token 即可获取。传入 YUQUE_TOKEN 到 yuque-hexo 的进程有两种方式：

- 设置全局的环境变量 YUQUE_TOKEN
- 命令执行时传入环境变量
  - mac / linux: `YUQUE_TOKEN=xxx yuque-hexo sync`
  - windows: `set YUQUE_TOKEN=xxx && yuque-hexo sync`

#### 配置知识库

> package.json

```json
{
  "name": "your hexo project",
  "yuqueConfig": {
    "postPath": "source/_posts/yuque",
    "cachePath": "yuque.json",
    "mdNameFormat": "title",
    "adapter": "hexo",
    "concurrency": 5,
    "baseUrl": "https://www.yuque.com/api/v2",
    "login": "yinzhi",
    "repo": "blog",
    "onlyPublished": false,
    "onlyPublic": false
  }
}
```

| 参数名        | 含义                                 | 默认值               |
| ------------- | ------------------------------------ | -------------------- |
| postPath      | 文档同步后生成的路径                 | source/\_posts/yuque |
| cachePath     | 文档下载缓存文件                     | yuque.json           |
| mdNameFormat  | 文件名命名方式 (title / slug)        | title                |
| adapter       | 文档生成格式 (hexo/markdown)         | hexo                 |
| concurrency   | 下载文章并发数                       | 5                    |
| baseUrl       | 语雀 API 地址                        | -                    |
| login         | 语雀 login (group), 也称为个人路径   | -                    |
| repo          | 语雀仓库短名称，也称为语雀知识库路径 | -                    |
| onlyPublished | 只展示已经发布的文章                 | false                |
| onlyPublic    | 只展示公开文章                       | false                |

> slug 是语雀的永久链接名，一般是几个随机字母。

### Install

```bash
npm i -g yuque-hexo
# or
npm i --save-dev yuque-hexo
```

### Sync

```
yuque-hexo sync
```

### Clean

```
yuque-hexo clean
```

### Npm Scripts

```json
{
  "sync": "yuque-hexo sync",
  "clean:yuque": "yuque-hexo clean"
}
```

### Debug

```
DEBUG=yuque-hexo.* yuque-hexo sync
```

### Best practice

- [Hexo 博客终极玩法：云端写作，自动部署](https://www.yuque.com/u46795/blog/dlloc7)
- [Hexo：语雀云端写作 Github Actions 持续集成](https://www.zhwei.cn/hexo-github-actions-yuque/)

> 另外 x-cold 本人提供了一个触发 Travis CI 构建的 HTTP API 接口，详情请查看[文档](https://github.com/x-cold/aliyun-function/tree/master/travis_ci) (请勿恶意使用)

### Notice

- 语雀同步过来的文章会生成两部分文件；

  - yuque.json: 从语雀 API 拉取的数据
  - source/\_posts/yuque/\*.md: 生成的 md 文件

- 支持配置 front-matter, 语雀编辑器编写示例如下:

  - 语雀编辑器示例，可参考[原文](https://www.yuque.com/u46795/blog/dlloc7)

  ```markdown
  tags: [hexo, node]
  categories: fe
  cover: https://cdn.nlark.com/yuque/0/2019/jpeg/155457/1546857679810-d82e3d46-e960-419c-a715-0a82c48a2fd6.jpeg#align=left&display=inline&height=225&name=image.jpeg&originHeight=225&originWidth=225&size=6267&width=225

  ---

  some description

  <!-- more -->

  more detail
  ```

- 如果遇到上传到语雀的图片无法加载的问题，可以参考这个处理方式 [#41](https://github.com/x-cold/yuque-hexo/issues/41)

### Example

- yuque to hexo: [x-cold/blog](https://github.com/x-cold/blog/blob/master/package.json)
- yuque to github repo: [txd-team/monthly](https://github.com/txd-team/monthly/blob/master/package.json)


## [Ocean](https://github.com/zhwangart/hexo-theme-ocean)

Ocean is a mobile-enabled Hexo theme based on the features in Hexo's default theme landscape. Since I am a Designer and not a Coder, so please advise! I am very grateful to [youchen1992](https://github.com/youchen1992) for providing technical support during the Ocean production process.


[Preview](https://zhwangart.github.io)

[中文说明](https://zhwangart.github.io/2018/11/30/Ocean/)

![Screenshot](screenshots/hexo-theme-ocean.jpg)

### Install

``` bash
$ git clone https://github.com/zhwangart/hexo-theme-ocean.git themes/ocean
```

### Enable

Modify `theme` setting in `_config.yml` to `ocean`

``` yml
theme: ocean
```

### Update

``` bash
cd themes/ocean
git pull
```

### Configuration

let me know if you can’t find something.

``` yml
# Menu
menu:
  Home: /
  Archives: /archives
  Gallery: /gallery
  About: /about
  Links: /links
rss: /atom.xml

# Miscellaneous
favicon: /favicon.ico
brand: /images/hexo.svg

# Ocean Video
# Because I put videos in multiple formats on the same path, I just labeled the path here.
ocean:
  overlay: true
  path: images/ocean/      # Video storage path, formats: mp4/ogg/webm
  brand: /images/hexo-inverted.svg      # Optional, a small logo

# Content
excerpt_link: Read More...

# fancybox
fancybox: true

# Local search
search_text: Search

# Gitalk
gitalk:
  enable: true
  clientID: # GitHub Application Client ID
  clientSecret: # Client Secret
  repo: # Repository name
  owner: # GitHub ID
  admin: # GitHub ID

# Valine
valine:
  enable: false    # Default: false.
  el: 'vcomments'    # The DOM element to be mounted on initialization.
  appId:    # Application appId from Leancloud.
  appKey:    # Application appKey from Leancloud.
  notify: false    # Mail notifier, Default: false.
  verify: true    # Validation code, Default: true.
  avatar: 'mp'    # Gravatar type.
  pageSize: '10'    # Number of pages per page.
  placeholder: '请输入...'    # Comment box placeholders.
```

The [feathericon](https://feathericon.com) in the menu is programmed ordely in "CSS `source/css/_partial/navbar.styl` " and can be changed or added if needed.

``` css
.nav-item
  &:nth-child(1)         // home
    .nav-item-link
      &::before
        content '\f12f'
  &:nth-child(2)         // archives
    .nav-item-link
      &::before
        content '\f12a'
  //&:nth-child(3)         // gallery
  //  .nav-item-link
  //    &::before
  //      content '\f1a9'
  //&:nth-child(4)         // about
  //  .nav-item-link
  //    &::before
  //      content '\f174'
```

### Plugins

+ [hexo-generator-search](https://github.com/hexojs/hexo-theme-landscape) Local search
	
  ```yml
  $ npm install hexo-generator-searchdb --save
  ```
  Then add the plugin configuration for hexo's configuration file `_config.yml` (note: not the theme's configuration file):
  
  ```yml
  # Hexo-generator-search
  search:
    path: search.xml
    field: post
    format: html
  ```

+ [hexo-generate-feed](https://github.com/hexojs/hexo-generator-feed) RSS

  ```yml
  $ npm install hexo-generator-feed --save
  ```
  
  Then add the plugin configuration for hexo's configuration file `_config.yml` (note: not the theme's configuration file):
  
  ```yml
  feed:
      type: atom
      path: atom.xml
      limit: 20
      hub:
      content:
      content_limit: 140
      content_limit_delim: ' '
      order_by: -date	
  ```
  
+ [hexo-generator-index-pin-top](https://github.com/netcan/hexo-generator-index-pin-top)
	
	``` bash
  $ npm uninstall hexo-generator-index --save
  $ npm install hexo-generator-index-pin-top --save
  ```

### Post poster

``` md
---
title: Post name

photos: [
        ["img_url"],
        ["img_url"]
        ]
---
```

### Gallery
Need to write in the head of the markdown, this is not a good way to write, I hope to get a better way to write on github.

``` md
---
title: Gallery

albums: [
        ["img_url","img_caption"],
        ["img_url","img_caption"]
        ]
---
```

### Toc

Use Tocbot to parse the title tags (h1~h6) in the content and insert the directory. 

+ ocean/_config.yml

	``` bash
	# Toc
  toc: true
	```
+ If Toc is turned on in ocean/_config.yml, then Tocbot will generate a Toc article directory in the title tag of each blog parsing content, but not all blogs require Toc, so in the Front-matter section of markdown Can be closed:

	``` md
	---
  toc: false
  ---
	```

---


