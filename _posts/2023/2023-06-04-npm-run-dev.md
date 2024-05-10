---
layout: mypost
title: 使用npm run dev 如何制定端口号
tags:
  - npm
---

> 我在vue的package.json中配置的scripts为 "dev": "vue-cli-service serve", 然后运行 npm run dev --port 8081，最后启动的项目访问端口还是默认的8080，自定义的8081端口未生效，是什么原因？
> 

使用 npm run dev --port 8081 命令指定端口号时，由于 --port 是 vue-cli-service serve 命令的参数，所以需要将 --port 放在 serve 参数之前，即在 npm run dev 命令后加入所需的端口号。例如，正确的命令为 npm run dev -- --port 8081。

在运行命令时，需要注意在 npm run dev 和 --port 8081 之间的双横线（--），其作用是告诉 npm 将 --port 8081 参数传递给 vue-cli-service serve 命令，而不是传递给 npm。如果没有加上双横线，端口号也将不会生效。

因此，需要将 package.json 中的 scripts 修改为 "dev": "vue-cli-service serve --port 8080"。然后在命令行运行 npm run dev -- --port 8081 即可启动项目并将端口号设置为 8081。


