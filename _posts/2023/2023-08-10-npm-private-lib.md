---
layout: mypost
title: npm私有库入门与实践
tags: 前端工程化
---

1. 目录
{:toc}

## 背景

在公司里面，很多项目都是基于相同的技术栈，那么也会有代码复用和组件复用的需求。但是目前各个项目的公共组件库和函数库部分都是项目各自拥有的，导致很多重复工作量。

为了提高工作效率，减少不必要的工作，于是开发一个公共组件库，发布在公司的 npm 服务器上，共享给全公司前端项目使用。

## 为什么要搭建 npm 私有库

npm 是 Node.js 的包管理器工具，也广泛应用于前端模块化开发，我们通过将代码以一定的模块化规范封装起来，上传到公共的 [npm 官方仓库](https://www.npmjs.com/)中，就可以方便的对代码进行共享与复用。

当我们需要使用 npm 仓库对公司业务代码进行复用封装，或者模块内部包含了一些敏感信息，此时公共仓库就不适用了，我们需要搭建自己的私有 npm 仓库。

## npm 私有库的好处

- **安全性角度**：如果我们想要一个公共组件库，那么把组件放到我们私有库中，只有内网可以访问，这样可以避免组件中业务的泄露；
- **模块复用性角度**：多个项目之间有重复的共有模块，当需要修改模块，通过简单的统一的配置就可以实现；提炼后的组件有专门的地址可以用来查看，方便使用，在后期项目的引用中也能节约开发成本
- **npm 包下载速度角度**：使用内部的地址，能够在开发下载 node 包的同时，将关联的依赖包缓存到 npm 私有仓库服务器中，下载速度更快；
- **项目开发中的路径角度**：在项目开发中书写代码更整洁简练，不需书写更长的相对路径；
- **公司技术沉淀角度**：知识的沉淀，在公司业务相关的应用上尤佳；
- **版本角度**：相当于一个容器，统一管理需要的包，保持版本的唯一；
- **开发效率角度**：使私有公共业务或组件模块能以共有包一样的管理组织方式，保持一致性，提高开发效率。

## npm 私有库原理

1、直接访问 npmjs 或者淘宝源

![](/image/2023/08/image_jnj0BUnKpA.png)

2、访问 npm 私有库

![](/image/2023/08/image_ScKE8X--kw.png)

![](/image/2023/08/image_AOwocIL9tG.png)

原理如下：用户使用 npm install 后向私有 npm 发起请求，服务器会先查询所请求的这个模块是否是我们自己的私有模块或已经缓存过的公共模块，如果是则直接返回给用户；如果请求的是一个还没有被缓存的公共模块，那么则会向上游源（上游的源可以是 npm 仓库，也可以是淘宝镜像）请求模块并进行缓存后返回给用户。

## npm 私有库选型

一般私有化的 npm 仓库有以下几种方法实现：

方式一：付费购买 npm 企业私有仓库

方式二：使用 git + ssh 这种方式直接引用到 GitHub 项目地址

方式三：通过开源项目直接搭建，例如 cnpm、verdaccio、sinopia 等

#### 方式二

npm 对于安装 git 仓库的命令

```bash
npm install <git remote url>
```

其中`git remote url`的格式是：

```js
<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish>]

很好理解，分别是： 协议://用户名:密码@主机域名:端口号/路径#提交记录或者是semver语义化规范

protocol 支持 git、git+ssh、git+http、git+https、git+file

<commit-ish> 安装的分支/commit/tag，默认值是 master.

比如：
npm i git+https://username:password@git.example.com/path/reposity#master
npm i git+https://username:password@git.example.com/path/reposity#1.0.0

```

换句话说，假设你的代码托管在`github`中，那么你就可以通过:

```bash
npm install git+https://github.com/pvorb/node-md5.git
npm install git+ssh://git@github.com:Daotin/private-npm.git

```

![](/image/2023/08/image_FCxX4RjbKZ.png)

![](/image/2023/08/image_OPtxdU4W4p.png)

在 package.json 中会自动增加一行：

```
"dependencies": {
  "md5": "git+https://github.com/pvorb/node-md5.git"
  "private-npm": "git+ssh://git@github.com/Daotin/private-npm.git"
}
```

这种方式唯一的不足的地方就是，你必须要确保安装这个私有模块的机器有访问这个私有模块 git 仓库的权限，换句话说就是：这台机器的公钥必须添加到 git 仓库/账户中。

如果你嫌添加公钥麻烦，也可以通过设置用户名和密码的方式，这种方式缺点就是用户名和密码都暴露出来了：

```bash
npm install git+https://Daotin:password@gitee.com/daotin/privete-npm.git
npm install git+ssh://git@gitee.com:daotin/privete-npm.git

```

![](/image/2023/08/image_m1P9Ap5bRl.png)

> 注意：下载的仓库一定要有 package.json 文件才可以下载成功。

**npm link 的使用**

虽然 npm + git 解决了我们内网私有模块的问题，但是大家想过没有，如果私有模块有点 bug 需要修复下，那么岂不是每次都要在私有模块项目进行修复，然后再打 tag 标签 push 到仓库，然后使用的项目再 npm install 安装下来，再去验证，好麻烦是不是。

`npm link` 可以帮助我们解决这样的麻烦问题。

```bash
# clone 私有包
git clone https://xxx.com/private-package.git
# 进入私有包目录
cd private-package
# 创建全局的link
npm link

# 进入项目目录
cd ../project/demo
# 将private-package link到项目
npm link private-package

# 取消link
npm unlink private-package
```

或者创建相对路径 link

```bash
cd ~/project/demo
# link 相对路径的 private-package
npm link ../private-package

# 取消相对路径的 private-package
npm unlink ../private-package
```

link 好之后，本地将私有包改几个字，发现就可以生效了，这样本地开发联调测试就非常方便了

#### 方式三（推荐）

可以搭建私有 npm 库的开源项目有下面这些：

- `Nexus` 是 Java 社区的一个方案，可以用于 Maven、npm 多种类型的仓库，界面比较丑。
- `Sinopia` 基于 Node.js 实现的一个开源 npm 库，年久失修。最近一次提交是 6 年前，直接放弃。
- `cnpm` 阿里出的 npm 私有方案，权限控制较为全面但是配置复杂，需要自己搭建 mysql 之类的数据库存储。
- `Verdaccio` 一个零配置、轻量型的私有 npm 模块管理工具，不需要额外的数据库配置，它内部自带小型数据库，支持私有模块管理的同时也支持缓存使用过的公共模块，发布及缓存的模块以静态资源形式本地存储，目前维护很频繁而且配置简单，可以快速搭建。

基于团队内使用的考虑，使用 Verdaccio 构建成本比较低，后期也好维护。

## **Verdaccio 框架介绍**

[Verdaccio](https://github.com/verdaccio/verdaccio "Verdaccio")是一个开源的 npm 私有仓库管理器，可以在本地搭建一个 npm 私有仓库并且进行管理。它可以用于个人、企业或组织内部的 npm 包管理，也可以用于在没有互联网连接的环境中搭建本地 npm 镜像，从而提高 npm 包的下载速度。

Verdaccio 的优势主要包括：

1.  私有仓库管理：可以在本地搭建私有仓库，提高安全性和可控性，可以控制谁可以访问和发布 npm 包。
2.  离线包管理：可以在没有互联网连接的环境中搭建本地 npm 镜像，从而提高 npm 包的下载速度。
3.  高度可定制：可以自定义插件和主题，以满足特定的需求。
4.  轻量级和易部署：可以在任何支持 Node.js 运行的机器上进行部署，只需简单的配置即可。
5.  高度兼容性：Verdaccio 兼容 npm 的所有特性和功能，可以无缝地与 npm 生态系统中的其他工具和库集成使用。
6.  易于使用：Verdaccio 提供了一个易于使用的 Web 界面，可以方便地管理 npm 包，也可以通过命令行工具进行管理。

## Verdaccio 安装与使用

### 安装

1、全局安装 verdaccio

```bash
npm install -g verdaccio

```

2、启动

```bash
# 如果没有特别的配置需求，使用默认配置启动也是一行命令
verdaccio

# 或者带参数启动
verdaccio --listen 4000 --config ~./config.yaml

```

默认配置创建目录为 `C:\Users\daotin\AppData\Roaming\verdaccio\config.yaml`默认监听 4873 端口

![](/image/2023/08/image_E6N2V8gdct.png)

打开 `localhost:4873` 会看到如下页面：

![](/image/2023/08/image_6nVq2YC3vY.png)

3、pm2 守护 verdaccio 进程

PM2 是一个帮助你管理并保持你的 Nodejs 应用 7/24 小时在线的后台程序

安装运行 pm2

```bash
# 安装
npm i -g pm2
# 运行
# 直接使用 pm2 start verdaccio，启动失败，因为找不到verdaccio
pm2 start `which verdaccio`

# 查看 pm2 守护下的进程 verdaccio 的实时日志
#pm2 list
```

> PS：网上说的`pm2 start verdaccio`和`pm2 start “which verdaccio”`都不行。

解决办法：

- `pm2 start C:\Users\lvonv\AppData\Roaming\npm\node_modules\verdaccio\bin`
- 设置环境变量
- 或者进入`verdaccio`目录，然后 `pm2 start verdaccio`

只有显示 online 才是成功启动。

![](/image/2023/08/image_bH_uVXZJcr.png)

### 自定义配置

> 官方文档：[https://verdaccio.org/zh-cn/docs/configuration/](https://verdaccio.org/zh-cn/docs/configuration/)

虽然，安装和启动好了 Verdaccio。但是，由于 Verdaccio 默认的配置和我们生产的需求不一致，所以我们需要修改一下 Verdaccio 的配置。

默认配置如下：

```bash
#
# This is the default configuration file. It allows all users to do anything,
# please read carefully the documentation and best practices to
# improve security.
#
# Look here for more config file examples:
# https://github.com/verdaccio/verdaccio/tree/5.x/conf
#
# Read about the best practices
# https://verdaccio.org/docs/best

# path to a directory with all packages
storage: ./storage
# path to a directory with plugins to include
plugins: ./plugins

# https://verdaccio.org/docs/webui
web:
  title: Verdaccio
  # comment out to disable gravatar support
  # gravatar: false
  # by default packages are ordercer ascendant (asc|desc)
  # sort_packages: asc
  # convert your UI to the dark side
  # darkMode: true
  # html_cache: true
  # by default all features are displayed
  # login: true
  # showInfo: true
  # showSettings: true
  # In combination with darkMode you can force specific theme
  # showThemeSwitch: true
  # showFooter: true
  # showSearch: true
  # showRaw: true
  # showDownloadTarball: true
  #  HTML tags injected after manifest <scripts/>
  # scriptsBodyAfter:
  #    - '<script type="text/javascript" src="https://my.company.com/customJS.min.js"></script>'
  #  HTML tags injected before ends </head>
  #  metaScripts:
  #    - '<script type="text/javascript" src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>'
  #    - '<script type="text/javascript" src="https://browser.sentry-cdn.com/5.15.5/bundle.min.js"></script>'
  #    - '<meta name="robots" content="noindex" />'
  #  HTML tags injected first child at <body/>
  #  bodyBefore:
  #    - '<div id="myId">html before webpack scripts</div>'
  #  Public path for template manifest scripts (only manifest)
  #  publicPath: http://somedomain.org/

# https://verdaccio.org/docs/configuration#authentication
auth:
  htpasswd:
    file: ./htpasswd
    # Maximum amount of users allowed to register, defaults to "+inf".
    # You can set this to -1 to disable registration.
    # max_users: 1000
    # Hash algorithm, possible options are: "bcrypt", "md5", "sha1", "crypt".
    # algorithm: bcrypt # by default is crypt, but is recommended use bcrypt for new installations
    # Rounds number for "bcrypt", will be ignored for other algorithms.
    # rounds: 10

# https://verdaccio.org/docs/configuration#uplinks
# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: https://registry.npmjs.org/

# Learn how to protect your packages
# https://verdaccio.org/docs/protect-your-dependencies/
# https://verdaccio.org/docs/configuration#packages
packages:
  '@*/*':
    # scoped packages
    access: $all
    publish: $authenticated
    unpublish: $authenticated
    proxy: npmjs

  '**':
    # allow all users (including non-authenticated users) to read and
    # publish all packages
    #
    # you can specify usernames/groupnames (depending on your auth plugin)
    # and three keywords: "$all", "$anonymous", "$authenticated"
    access: $all

    # allow all known users to publish/publish packages
    # (anyone can register by default, remember?)
    publish: $authenticated
    unpublish: $authenticated

    # if package is not available locally, proxy requests to 'npmjs' registry
    proxy: npmjs

# To improve your security configuration and  avoid dependency confusion
# consider removing the proxy property for private packages
# https://verdaccio.org/docs/best#remove-proxy-to-increase-security-at-private-packages

# https://verdaccio.org/docs/configuration#server
# You can specify HTTP/1.1 server keep alive timeout in seconds for incoming connections.
# A value of 0 makes the http server behave similarly to Node.js versions prior to 8.0.0, which did not have a keep-alive timeout.
# WORKAROUND: Through given configuration you can workaround following issue https://github.com/verdaccio/verdaccio/issues/301. Set to 0 in case 60 is not enough.
server:
  keepAliveTimeout: 60
  # Allow `req.ip` to resolve properly when Verdaccio is behind a proxy or load-balancer
  # See: https://expressjs.com/en/guide/behind-proxies.html
  # trustProxy: '127.0.0.1'

# https://verdaccio.org/docs/configuration#offline-publish
# publish:
#   allow_offline: false

# https://verdaccio.org/docs/configuration#url-prefix
# url_prefix: /verdaccio/
# VERDACCIO_PUBLIC_URL='https://somedomain.org';
# url_prefix: '/my_prefix'
# // url -> https://somedomain.org/my_prefix/
# VERDACCIO_PUBLIC_URL='https://somedomain.org';
# url_prefix: '/'
# // url -> https://somedomain.org/
# VERDACCIO_PUBLIC_URL='https://somedomain.org/first_prefix';
# url_prefix: '/second_prefix'
# // url -> https://somedomain.org/second_prefix/'

# https://verdaccio.org/docs/configuration#security
# security:
#   api:
#     legacy: true
#     jwt:
#       sign:
#         expiresIn: 29d
#       verify:
#         someProp: [value]
#    web:
#      sign:
#        expiresIn: 1h # 1 hour by default
#      verify:
#         someProp: [value]

# https://verdaccio.org/docs/configuration#user-rate-limit
# userRateLimit:
#   windowMs: 50000
#   max: 1000

# https://verdaccio.org/docs/configuration#max-body-size
# max_body_size: 10mb

# https://verdaccio.org/docs/configuration#listen-port
# listen:
# - localhost:4873            # default value
# - http://localhost:4873     # same thing
# - 0.0.0.0:4873              # listen on all addresses (INADDR_ANY)
# - https://example.org:4873  # if you want to use https
# - "[::1]:4873"                # ipv6
# - unix:/tmp/verdaccio.sock    # unix socket

# The HTTPS configuration is useful if you do not consider use a HTTP Proxy
# https://verdaccio.org/docs/configuration#https
# https:
#   key: ./path/verdaccio-key.pem
#   cert: ./path/verdaccio-cert.pem
#   ca: ./path/verdaccio-csr.pem

# https://verdaccio.org/docs/configuration#proxy
# http_proxy: http://something.local/
# https_proxy: https://something.local/

# https://verdaccio.org/docs/configuration#notifications
# notify:
#   method: POST
#   headers: [{ "Content-Type": "application/json" }]
#   endpoint: https://usagge.hipchat.com/v2/room/3729485/notification?auth_token=mySecretToken
#   content: '{"color":"green","message":"New package published: * {{ name }}*","notify":true,"message_format":"text"}'

middlewares:
  audit:
    enabled: true

# https://verdaccio.org/docs/logger
# log settings
log: { type: stdout, format: pretty, level: http }
#experiments:
#  # support for npm token command
#  token: false
#  # disable writing body size to logs, read more on ticket 1912
#  bytesin_off: false
#  # enable tarball URL redirect for hosting tarball with a different server, the tarball_url_redirect can be a template string
#  tarball_url_redirect: 'https://mycdn.com/verdaccio/${packageName}/${filename}'
#  # the tarball_url_redirect can be a function, takes packageName and filename and returns the url, when working with a js configuration file
#  tarball_url_redirect(packageName, filename) {
#    const signedUrl = // generate a signed url
#    return signedUrl;
#  }

# translate your registry, api i18n not available yet
# i18n:
# list of the available translations https://github.com/verdaccio/verdaccio/blob/master/packages/plugins/ui-theme/src/i18n/ABOUT_TRANSLATIONS.md
#   web: en-US

```

这里我们来逐个认识一下默认配置中的几个值的含义：

- `storage` 已发布的包的存储位置，默认存储在 `~/.config/Verdaccio/` 文件夹下
- `plugins` 插件所在的目录
- `web` 界面相关的配置
- `auth` 用户相关，例如注册、鉴权插件（默认使用的是 `htpasswd`）
- `uplinks` 用于提供对外部包的访问，例如访问 npm、cnpm 对应的源
- `packages` 用于配置发布包、删除包、查看包的权限
- `server` 私有库服务端相关的配置
- `middlewares` 中间件相关配置，默认会引入 `auit` 中间件，来支持 `npm audit` 命令
- `logs` 终端输出的信息的配置

接下来，我们就来修改 Verdaccio 的配置文件中对应的值来一一支持上述功能。

#### storage

用于指定缓存 package 的存放路径，当你用 npm 将代码推送到仓库中，或是从上游仓库查找 package 时，都会将最终的 package 存放到此目录中。

#### auth

系统默认权限认证使用`htpasswd`，htpasswd 中存储者用户名和加密过的秘钥

#### **packages**

控制访问包的方式

```bash
packages:
  '@*/*':
    access: $all
    publish: $authenticated
    proxy: private1

```

上述配置控制的是仓库里的包的访问行为

- `@*/*` 表示接下来的配置是针对 scoped 的包，如 `@vue/some-package`
- `@vue/*` 表示接下来的配置针对 @vue 这个 `scope` 下的任意库的
- `vue-*` 表示接下来的配置针对`以vue-开头`的库
- `**` 表示接下来的配置针对任意库

**权限控制**

- `access` 包的访问权限
- `publish` 包的发布权限
- `unpublish` 控制包的删除权限
- `proxy` 控制从哪个下载源(在`uplink`配置的下载源)

权限有 3 个可选值

- `$all`（所有人）
- `$anonymous`（未注册用户）
- `$authenticated`（注册用户）

#### **uplinks**

`uplinks` 类似于 git 的 `remote`，说白了就是私库的上游是什么，当私库中不存在用户要安装的包时，就会继续往上游拉取。

```bash
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
  cnpm:
    url: https://url.of.cnpm.org/
  private1:
    url: https://192.168.0.100:6999/

```

`uplinks` 可以在 `packages` 中，根据不同组的下载需求，配置 proxy。

#### 其他配置

verdaccio 在文件系统上存储数据，没有额外依赖，而且提供了一套默认配置，我们可以直接启动仓库服务，不过最好事先将需要的文件及文件夹创建好：

```bash
# 缓存包的存放目录
$ mkdir -p storage

# 配置文件
$ touch config.yaml

# 储存auth信息的文件
$ touch htpasswd

# verdaccio的日志文件
$ touch verdaccio.log
```

#### 配置示例

```bash
# 设置托管或缓存包的存放目录
storage: ./storage
# 权限控制
auth:
  # 默认使用htpasswd做权限控制
  htpasswd:
    # 制定 htpasswd 文件路径，htpasswd 中存储者用户名和加密过的秘钥
    file: ./htpasswd
    # 最多允许注册用户数。设置为-1则禁止注册
    max_users: 1000
# 备用仓库，如果 verdaccio 找不到，就会用到这里的仓储
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
  yarnjs:
    url: https://registry.yarnpkg.com/
  cnpmjs:
    url: https://registry.npm.taobao.org/
# 包访问或发布的规则
packages:
  '@*/*':
    access: $all
    publish: $authenticated
    proxy: npmjs
  '**':
    access: $all
    publish: $authenticated
    proxy: npmjs
# 日志配置
logs:
  - { type: file, path: verdaccio.log, level: info }
# 开启web模式
web:
  title: npm-private-lib

# 监听的端口，不配置这个，只能本机能访问 # listen on all addresses (INADDR_ANY)
listen: 0.0.0.0:4873

# 界面默认设为中文
i18n:
  web: zh-CN

```

### 注册与发布

1、注册用户

```bash
npm adduser --registry http://localhost:4873/

```

输入 username、password 以及 Email 即可

错误提示：`[FORBIDDEN] Public registration is not allowed`

解决办法：切换到 npm 原始镜像`npm config set registry `[https://registry.npmjs.org/](https://registry.npmjs.org/ "https://registry.npmjs.org/")

2、登录

```bash
npm login --registry http://localhost:4873

```

3、上传私有包

```bash
# 在当前私有仓库下执行
npm publish --registry http://localhost:4873

```

![](/image/2023/08/image_RPyLdjHqt_.png)

> 注意：
> 1、当前私有仓库必须包含 package.json 文件
> 2、每次发布的时候，都需要使用`npm version v1.x.x `更新版本，并且保证仓库是干净的

![](/image/2023/08/image_l3edo28rvZ.png)

4、移除一个包

```js
// 删除特定版本
npm unpublish <package-name>@<version>

// 删除整个包（谨慎使用）：
npm unpublish <package-name> --force

```

请注意，如果你要删除整个包，必须使用 `--force` 标志。

### 使用方式

和使用淘宝源一样，本质上是在使用 npm 命令的时候，更改 registry 配置，指定要安装的包的来源，常用的有两种方式

1、在 npm 参数中指定源

```bash
$ npm install <package> --registry='http://localhost:4873'
# 更进一步，可以把这条指令抽取成 alias
export mynpm="mynpm --registry='http://localhost:4873'"
# 每次需要运行 npm 的时候就运行 mynpm

```

2、将 npm 镜像源切换至私有 npm 库

```bash
$ npm set registry http://localhost:4873

```

但是如果我们想再次切换到淘宝或者其他的镜像地址，就不那么方便了；我们可以通过`nrm`这个工具来管理我们的源地址，可以查看和切换地址：

```bash
npm install -g nrm
```

安装后我们可以通过`nrm add [name] [address]`这个命令来新增一个源地址：

```bash
nrm add localnpm http://localhost:4873/
```

使用`nrm ls`可以查看我们使用的所有源地址，带`*`是正在使用的地址；通过`nrm use [name]`来切换地址：

![](/image/2023/08/image_ZznnziQAVm.png)

3、安装使用私有包 mylog

![](/image/2023/08/image_w9Bp3qyWtM.png)

## 参考文章

- [https://www.cnblogs.com/zyfenblog/p/17292547.html](https://www.cnblogs.com/zyfenblog/p/17292547.html)
- [https://segmentfault.com/a/1190000040902193](https://segmentfault.com/a/1190000040902193)
- [https://zhuanlan.zhihu.com/p/112063852](https://zhuanlan.zhihu.com/p/112063852)
