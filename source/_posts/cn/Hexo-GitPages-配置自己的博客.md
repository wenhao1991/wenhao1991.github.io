---
title: Hexo + GitPages 配置自己的博客
catalog: true
lang: cn
date: 2023-06-24 21:46:01
subtitle:
header-img: /img/header_img/lml_bg8.jpg
tags: 
    - 工具
    - 配置
categories:
    - 工具
---

> 一直想要有一个自己的博客，又不想在配置上花太多时间，经过一番调研，发现hexo+markdown写博客，一键托管到gitpages上可以完美实现我的需求，配置也不复杂。
> 同时为博客申请一个域名，后续因为网络/容量原因不再托管到github上，也可以无痛切换。

> 环境：windows10

## 为什么选择hexo
- 支持markdown语法
- 支持github一键部署
- 有详实的文档和精美的主题可选


## hexo 安装和部署
- [官方文档](https://hexo.io/zh-cn/docs/)
- 安装 Node.js (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)
    - https://nodejs.org/en
    - 使用 Node.js 官方安装程序时，请确保勾选 Add to PATH 选项（默认已勾选）
- 安装git
    - https://git-scm.com/download/win
- 安装hexo
    - shift + 鼠标邮右键 -> 在此处打开Powershell窗口 
    - npm install -g hexo-cli
- 开通执行脚本权限
    - 在刚才的窗口中执行hexo 如果出现以下问题
    > `hexo : 无法加载文件 C:\Users\wenhao\AppData\Roaming\npm\hexo.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。`
    - 左下角搜索 -> Powershell -> 以管理员打开 -> set-ExecutionPolicy RemoteSigned -> A[全是]
    - 此时再执行hexo, 应该会返回
    > Usage: hexo command

## hexo搭建博客
- 在想放置博客相关文件的地方创建一个文件夹 `<folder>`
- PowerShell 依次执行
```
    hexo init <folder>
    cd <folder>
    npm install
```

- 生成的仓库目录如下
```
├── _config.yml     # 主要配置文件
├── package.json
├── scaffolds       # 模板文件
├── source          # 博客的md和相关资源放这里
|   ├── _drafts         # 草稿
|   └── _posts          # 已发表
└── themes          # 主题文件
```

- 本地调试博客
    - 在博客目录`<folder>`下，打开PowerShell(shift+右键->在此处打开Powershell窗口)执行
    ```
        hexo g      # 生成博客相关文件到public文件夹
        hexo s      # 本地部署博客到 http://localhost:4000/, hexo s -p XX 自定义端口
    ```
    - 浏览器打开 http://localhost:4000/ 即可预览博客效果

## 选择主题
- [hexo主题页面](https://hexo.io/themes/) 有大量主题可供选择
- 点击图片可以预览主题效果
- 点击主题名可以进入对应的github仓库
    - 每个主题根据对应的ReadMe一步步配置即可
- 我选择的是[hexo-theme-livemylife](https://v-vincen.life/en/Hexo-Theme-LiveMyLife/)
    - 主题精美
    - 内嵌了gitalk, busuanzi等多个控件，无需额外安装
    - 支持多语言

## 一键部署到gitpage
gitpage是github提供的静态网页托管服务
- 在github上建立名为 <你的 GitHub 用户名>.github.io 的仓库
```
    如 myname.github.io
```
- 安装  hexo-deployer-git
```     
    npm install hexo-deployer-git --save
```
- 修改 _conifg.yml, 增加deploy
```
    deploy:
        type: git
        repo: <上述仓库地址> # 如 git@github.com:myname/myname.github.io.git
        branch: [branch]    # 分支名
        message: [message]  # 提交信息
```
- 修改github仓库中，自动触发部署博客的分支(默认master)
    - 上述deploy中的branch 如果在分支中不存在，先创建分支
    - 在github仓库中 setting -> Pages -> Build and deployment
    - branch改为deploy中定义的branch
- 一键部署
    - 博客目录中，在powershell 中执行 `hexo deploy`
    - 在github的仓库内查看并等待ci部署流程完成
    - 打开网址 <你的 GitHub 用户名>.github.io 查看博客
- 原理
    - hexo g 会从source生成public文件夹
    - public文件夹 是我们访问到的网页所在的文件夹
    - hexo d 会自动从source生成public, 并提交到github仓库的[branch]分支上
    - 我们修改配置可以让该分支的提交自动触发部署博客到gitpages

## 博客评论
- 我是使用的 [hexo-theme-livemylife](https://v-vincen.life/en/Hexo-Theme-LiveMyLife/) 主题集成的 [Gitalk](https://github.com/gitalk/gitalk)
    - 使用 GitHub 登录
    - 支持多语言 [en, zh-CN, zh-TW, es-ES, fr, ru]
    - 支持个人或组织
    - 无干扰模式（设置 distractionFreeMode 为 true 开启）
    - 快捷键提交评论 （cmd|ctrl + enter）
- 在 hexo-theme-livemylife 主题中，只需要
    - 安装gitalk：`npm i --save gitalk`
    - 申请一个github仓库用于存储评论（最好自己现在issue先评论一条）
    - 申请 [GitHub Application](https://github.com/settings/applications/new)
        - 申请好会有一个ClientID 和 ClientSecret
    - 修改_config.yml
    - 部署：`hexo d` 
    ```yml
    gitalk:
        owner:      # 上面申请的仓库用户名，如  yourname
        admin:      # 上面申请的仓库用户名，如  [yourname]
        repo:       # 上面申请的仓库仓库名，如  comment
        clientID:   # GitHub Application Client ID'
        clientSecret:   # 'GitHub Application Client Secret'
        perPage: 10     # Pagination size, with maximum 100.
        pagerDirection: last          # Comment sorting direction, available values are last and first.
        createIssueManually: false      # By default, Gitalk will create a corresponding github issue for your every single page automatically when the logined user is belong to the admin users. You can create it manually by setting this option to true
        language: en                    # Localization language key, en, zh-CN and zh-TW are currently available.
        maxCommentHeight: 250           # An optional number to limit comments' max height, over which comments will be folded.Default 250.
        proxy: https://cors-anywhere.azm.workers.dev/https://github.com/login/oauth/access_token  # GitHub oauth request reverse proxy for CORS. For example, the demo url is 'https://cors-anywhere.herokuapp.com/https://github.com/login/oauth/access_token'.You should deploy your own proxy url as in this issue https://github.com/gitalk/gitalk/issues/429.
    ```

### Gitalk 安装
- 直接引入
```html
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
  <script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>

  <!-- or -->

  <link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
  <script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
```
- npm 安装
```sh
npm i --save gitalk
```

```js
import 'gitalk/dist/gitalk.css'
import Gitalk from 'gitalk'
```

### Gitalk 使用
因为我用的主题中已集成Gitalk，修改配置即可。从头配置的同学可以参考[官方中文文档](https://github.com/gitalk/gitalk/blob/master/readme-cn.md)

## 域名申请和配置
为了防止后续有迁移博客的需求（如github被墙，或者博客仓库超过1G了等等）
我申请了一个域名指向gitpages上的博客地址，后续需要迁移也可以无痛切换。

- 域名申请
    - [阿里云域名](https://wanwang.aliyun.com/?utm_content=se_1013632823) 搜索自己想要的域名是否可用，并加入清单
    - 在域名清单中选择域名+云解析DNS个人版 购买，100元左右一年（似乎没买个人版DNS也会被解析，没试过不敢确定）
    - 付款前需要创建个人模板，提供个人资料审核，我15分钟就审核好了
- 域名解析
    - 假设你申请的域名是 www.yourname.com
    - 进入[域名解析]（https://dns.console.aliyun.com/?spm=5176.12818093.0.dre0.385616d0DOTWFE#/dns/domainList）页面
    - 点击【解析设置】-> 添加记录, 创建两条记录
    - ![解析](域名解析添加记录.png)
    - 记录1，用于解析不带www的地址，如 yourname.com
        - 记录类型 选 **CNAME**
        - 主机记录 选 **@**
        - 解析请求来源 选 **默认**
        - 记录值，填 **想要指向的地址**，如你的gitpage上的博客 <你的 GitHub 用户名>.github.io
    - 记录2，用于解析带www的地址，如 www.yourname.com
        - 其他不变，主机记录 选 **www**
- 配置修改
    - 博客仓库的 source文件夹下新增 CNAME文件
    - CNAME文件第一行：`yourname.com`
    - 部署：hexo d
- 等gitpage自动部署完成之后，访问`yourname.com` 或者 `www.yourname.com` 就会指向你托管到github上的博客了。
