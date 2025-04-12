+++
date = '2025-04-11T11:31:52-07:00'
draft = false
title = 'My First Post'

+++

# Hugo+GitHub Pages搭建博客
<!--more-->

## Hugo搭建博客

### 安装Hugo及Git

- 安装**Hugo**并确认

  ```
  sudo snap install hugo
  hugo version
  ```

- 安装**Git**

  ```
  sudo apt update
  
  sudo apt install git
  ```

### 创建网站

- 在 `blog` 目录下创建项目目录结构

  `hugo new site blog`

- 切换到项目根目录

  `cd blog`

- 初始化一个Git仓库

  `git init`
  
- 将 Ananke 主题克隆到 `themes` 目录中，将其作为 Git 子模块添加到的项目中

  该主题项目里详细介绍了各种参数的使用

  ```text
  git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
  ```

  可以[在这里](https://themes.gohugo.io/)找到更多的主题

  注意事项

  - 子模块的代码变更需要在其目录内单独提交（类似于独立仓库）。
  - 如果删除子模块，需手动删除 `.gitmodules` 和 `.git/modules/themes/ananke` 中的配置。

- 将一行内容追加到网站配置文件中，指明当前主题

  `echo "theme = 'ananke'" >> hugo.toml`

- 启动 Hugo 服务器以查看网站

  `hugo server`

### 添加内容

- 为网站添加页面，在 `content/posts` 目录下创建文件

  `hugo new content content/posts/my-first-post.md`

  用编辑器打开该文件，可以看到以下内容

  ```
  +++
  date = '2025-04-11T11:31:52-07:00'
  draft = true
  title = 'My First Post'
  +++
  ```
  
  
  
  以下是一个文章的例子，随便加点东西
  
  ```
  +++
  date = '2025-04-11T11:31:52-07:00'
  draft = true
  title = 'My First Post'
  
  +++
  
  # My First Post
  - hello,lyp
  ```
  
  
  
- 文章属性说明
  

  - **date** 自动增加时间标签，页面上默认显示 n 篇最新的文章。
  - **draft** 设置为 false 的时候会被编译为 HTML，true 则不会编译和发表，在本地修改文章时候用 true。默认情况下，Hugo 在构建网站时不会发布草稿内容。
  - **title** 设置文章标题
  
- 启动Hugo服务器查看网站，可以使用以下任一命令来显示草稿内容（draft值为true）

  ```
  hugo server --buildDrafts
  hugo server -D
  ```

  在浏览器打开终端显示的 URL。保持Hugo服务器运行，添加和更改内容时，网站会自动发生改变。

  修改到满意时，可以将draft值改为true。执行以下命令

  `hugo server`

### 配置网站

使用编辑器，在项目根目录下打开站点配置文件（ `hugo.toml` ）

```
baseURL = 'https://example.org/'
languageCode = 'en-us'
title = 'My New Hugo Site'
theme = 'ananke'
```

- 设置生产站点的 `baseURL` 。此值必须以协议开头，以斜杠结尾，如上所示
- 设置 `languageCode` 为所在的语言和地区
- 设置您生产网站的 `title` 

### 发布网站

执行以下命令

`hugo`

Hugo 会在项目根目录下的 `public` 目录中创建整个静态网站。这包括 HTML 文件以及图像、CSS 文件和 JavaScript 文件等资源。不包含 [draft, future, or expired](https://gohugo.io/getting-started/usage/#draft-future-and-expired-content)的内容。



**参考链接** 

[hugo快速开始](https://gohugo.io/getting-started/quick-start/)

[hugo项目地址](https://github.com/gohugoio/hugo)





### 其他



在markdown文件中添加`<!--more-->`可以使文章列表页仅显示分隔符前的内容



## GitHub Pages托管

### **创建专属仓库**

- 访问 [GitHub官网](https://github.com/)，登录后，点击右上角 **+** → **New repository**。
- **仓库名格式**：必须为 `你的用户名.github.io`（如用户名为 `john`，则仓库名为 `john.github.io`）。
- 点击 **Create repository**。

### 推送到远程仓库地址

本地仓库将文件添加到暂存区

```
git add .          # 添加所有文件
```

提交更改（提交后才会创建默认分支）

```
git commit -m "Initial commit"
```

本地添加远程仓库（默认名为origin）

```
git remote add origin https://github.com/你的用户名.github.io.git
```

如果已存在 `origin`，可先删除旧地址

```
git remote remove origin
```

推送本地master 分支推送到远程 main 分支，本地的分支默认为master

```
git push -u origin master:main  # 将本地 master 分支推送到远程 main 分支
```

登录需要用令牌来登录

- 在 GitHub 任何页面的右上角，点击个人头像，然后点击设置。
- 在左侧边栏中，点击**Developer Settings**。
- 在左侧边栏中，点击**“Personal access token”**下的**”Personal access tokens (classic)“**
- 
  选择“生成新令牌”，然后点击“生成新令牌

### 部署到githubpages

Hugo 提供非常详尽的 [GitHub Pages 部署指引](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
