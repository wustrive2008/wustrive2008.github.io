title: hexo博客搭建记录
date: 2015-03-21 21:35:25
tags: [hexo,pacman,github]
categories: hexo
---

# 下面是本博客的搭建记录

## 准备

### 软件环境
1. windows 8.1
2. nodejs  [官网](https://nodejs.org/)
3. git     [官网](http://git-scm.com/download/)

### 注册github 账号
1. 注册github账号 [官网](https://github.com/)
2. 创建博客仓库
3. 添加ssh-key

建立与你用户名对应的仓库，仓库名必须为『your_user_name.github.com』

![github blog](/img/githubblog.PNG)




## 安装Hexo 参考[hexo官网](http://hexo.io/zh-cn/docs/index.html)

### cmd中输入安装命令即可完成安装

```
$ npm install -g hexo-cli
```

## 博客搭建

### 建站
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```
$ hexo init <folder>
$ cd <folder>
$ npm install
```

配置文件_config.yml

```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: wustrive's Hexo blog
subtitle: 生活，工作记录
description: "hello,I'm wustrive. This is my blog on GitHub."
author: wustrive
language: zh-CN
email: wustrive_2008@126.com
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://wustrive2008.github.io
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: pacman
stylus:
    compress: true


# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:wustrive2008/wustrive2008.github.io.git
  branch: master

plugins:
- hexo-generator-feed
- hexo-generator-sitemap

```



### 安装pacman主题 [github地址](https://github.com/A-limon/pacman)

#### 安装 (切换到博客目录)
```
$ git clone https://github.com/A-limon/pacman.git themes/pacman

```

#### 启用

修改你的博客根目录下的config.yml配置文件中的theme属性，将其设置为pacman。同时请设置stylus属性中的compress值为true。

#### 更新 (更新前建议备份_config..yml文件)
```
cd themes/pacman
git pull

```

#### 配置文件_config.yml
```
##### Menu
menu:
  主页: /
  归档: /archives
## you can create `tags` and `categories` folders in `../source`.
## And create a `index.md` file in each of them.
## set `front-matter`as
## layout: tags (or categories)
## title: tags (or categories)
## ---

#### Widgets
widgets: 
- category
- tag
- rss
## provide six widgets:category,tag,rss,archive,tagcloud,links.
## modify links in `/layout/_widget/links.ejs`.

#### RSS
rss: /atom.xml ## RSS address.

#### Image
imglogo:
  enable: true             ## display image logo true/false.
  src: img/logo.svg        ## `.svg` and `.png` are recommended,please put image into the theme folder `/pacman/source/img`.
favicon: img/favicon.ico   ## size:32px*32px,`.ico` is recommended,please put image into the theme folder `/pacman/source/img`.     
apple_icon: img/pacman.jpg ## size:114px*114px,please put image into the theme folder `/pacman/source/img`.

#### Author Avatar Picture
author_img_enable: true ## display author avatar picture
dataURI: false
## if the picture's format is dataURI please set the value to true,otherwise set the value to false.
## convert an image into base 64 data URIs http://websemantics.co.uk/online_tools/image_to_data_uri_convertor/ .
author_img_data: ''
## paste the dataURI in ONE LINE and included it by ''.
author_img: ../img/author.jpg ## size:220px*220px.
## if the picture's format is `.png` or `.jpg`  instead of dataURI,you should set the `dataURI` value to false.

#### Font
ShowCustomFont: true  
## you can change custom font in `variable.styl` and `font.styl` which in the theme folder `/pacman/source/css`.

#### Toc
toc:
  article: true   ## show contents in article.
  aside: true     ## show contents in aside.
## you can set both of the value to true of neither of them.
## if you don't want display contents in a specified post,you can modify `front-matter` and add `toc: false`.

#### Fancybox
fancybox: false 
## if you use gallery post or want use fancybox please set the value to true.
## if you want use fancybox in ANY post please copy the file `fancybox.js`.
## in theme folder `/pacman/scripts` to your hexo blog folder `../scritps`.

#### Author information
author:
  google_plus:    ## eg:116338260303228776998 for https://plus.google.com/u/0/116338260303228776998
  intro_line1: "Hello, I'm wustrive. This is my blog on GitHub." ## eg: "Hello ,I'm Larry Page in Google."
  intro_line2: "I wish you happiness" ## eg: "This is my blog,believe it or not."
  weibo:      12100106  ## e.g. 436062867 for http://weibo.com/436062867
  twitter:    wustrive ## e.g. yangjiansky for https://twitter.com/yangjiansky
  github:     wustrive2008   ## e.g. A-limon for https://github.com/A-limon
  facebook:   ## e.g. yangjian for https://favebook.com/yangjian
  tsina:      ## e.g. 1664838973  Your weibo ID,It will be used in share button.
  linkedin:   ## e.g. in/jeffweiner08 for https://www.linkedin.com/in/jeffweiner08

#### Comment
duoshuo: 
  enable: true  ## duoshuo.com
  short_name: wustrive ## duoshuo short name.
highlight:
  enable: true
  line_number: true
  tab_replace:

#### Share button
jiathis:
  enable: false ## if you use jiathis as your share tool,the built-in share tool won't be display.
  id:    ## e.g. 1501277 your jiathis ID. 
  tsina: ## e.g. 1664838973 Your weibo id,It will be used in share button.


#### Baidu Analytics
baidu_analytics: true


#### Analytics
google_analytics:
  enable: false
  id:   ## e.g. UA-1766729-8 your google analytics ID.
  site: ## e.g. yangjian.me your google analytics site or set the value as auto.
## You MUST upgrade to Universal Analytics first!
## https://developers.google.com/analytics/devguides/collection/upgrade/?hl=zh_CN

#### Custom Search
google_cse: 
  enable: false
  cx:  ## e.g. 000561263943549425496:mrzrm0gr4kg your Custom Search ID
## https://www.google.com/cse/ 
## To enable the custom search You must create a "search" folder in '/source' and a "index.md" file
## set the 'front-matter' as
## layout: search 
## title: search
## ---

```


## 备注

### hexo插件安装 ,比如安装git插件

```
$ npm install hexo-deployer-git --save

```

### 代码高亮不显示问题

将主配置文件中的：
```
highlight:
  enable: true
  line_number: true
  tab_replace:

```
拷贝到主题配置的_config.yml文件中

### pacman主题author图片显示问题

修改主题的_config.yml文件中的author_img配置:
```
author_img: ../img/author.jpg 

```




