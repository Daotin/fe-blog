---
title: 绑定Github上的个人博客到Godaddy域名
tags: blog
---

1. 目录
{:toc}

## 一、首先在[Godaddy官网](https://sg.godaddy.com/zh/)购买域名

## 二、配置Github

### 1、在我们的Hexo项目的sources目录下新建个**CNAME**文件，里面写上我们的域名。

![7](https://user-images.githubusercontent.com/23518990/68289256-c52cf480-00c0-11ea-917d-eb4cccebd142.jpg)

<!--more-->

之后重新部署项目：

```
hexo g
hexo d
```

> 如果你是用 hexo 框架搭建博客并部署到 Github Pages 上:
>
> 每次hexo g  hexo d 后会把你的博客所在目录下 public 文件夹里的东西都推到 Github Pages 仓库上，并且把 CNAME 文件覆盖掉，解决这个问题可以直接**把 CNAME 文件添加到 source 文件夹里**，这样每次推的时候就不用担心仓库里的 CNAME 文件被覆盖掉了。



之后我们可以在网站的Github项目的根目录看到这个文件：

![8](https://user-images.githubusercontent.com/23518990/68289274-cc540280-00c0-11ea-97e0-0937492eeeef.jpg)




> *还有一种方式是：在网站的Github项目上，点击设置Settings，找到Custom domain，填入申请的域名，并保存。这样也会在Github项目的根目录看到这个文件，但是当你在每次部署项目之后，这个CNAME文件都会消失，本质上相当于你新建的CNAME放在了本地Github项目的根目录了，而不是在source文件夹下。*

![9](https://user-images.githubusercontent.com/23518990/68289287-d118b680-00c0-11ea-9ec5-9e095cb9a602.jpg)

### 2、向你的 DNS 配置中添加 3 条记录（在域名解析提供商，下面以dnspod为例）

| Host(主机记录) | 记录类型  | Points To(记录值)     |      |
| ---------- | ----- | ------------------ | ---- |
| @          | A     | 192.30.252.153     |      |
| @          | A     | 192.30.252.154     |      |
| www        | CNAME | username.github.io |      |


> 这样别人用www和不用www都能访问你的网站（其实www的方式，会先解析成http://xxxx.github.io，         然后根据CNAME再变成http://xxx.com， 即中间是经过一次转换的）。
> 上面，我们用的是CNAME别名记录，也有人使用A记录，后面的记录值是写github page里面的ip地址，但有时候IP地址会更改，导致最后解析不正确，所以还是推荐用CNAME别名记录要好些，不建议用IP。
> 如：
> （1）先添加一个CNAME，主机记录写@，后面记录值写上你的http://xxxx.github.io
> （2）再添加一个CNAME，主机记录写www，后面记录值也是http://xxxx.github.io 
>
> 用你自己的 Github 用户名替换 username.




### 3、去 GoDaddy 修改 DNS 地址

（1）在右上角我的账户下拉菜单中，点击-> 我的产品：

![10](https://user-images.githubusercontent.com/23518990/68289313-da098800-00c0-11ea-99ed-4aa3e61c8325.jpg)



（2）点击域名后面的 DNS 按钮：

![11](https://user-images.githubusercontent.com/23518990/68289322-dd9d0f00-00c0-11ea-9f71-a15c1bf3c3b3.jpg)



（3）更改域名服务器为：

```
f1g1ns1.dnspod.net 
f1g1ns2.dnspod.net
```

![12](https://user-images.githubusercontent.com/23518990/68289331-e1309600-00c0-11ea-8382-47500fb31170.jpg)


（4）等待你的 DNS 配置生效：

对DNS的配置不是立即生效的，过1分钟再去访问你的域名看看有没有配置成功。



# 三、参考资料

- [知乎：github怎么绑定自己的域名？](https://www.zhihu.com/question/31377141)
- [如何搭建一个独立博客——简明Github Pages与Hexo教程 - 简书]( http://www.jianshu.com/p/05289a4bc8b2)
- [通过GitHub和GoDaddy搭建静态个人博客 - openxxs - 博客园]( http://www.cnblogs.com/openxxs/p/5950598.html?utm_source=itdadao&utm_medium=referral)





（完）

