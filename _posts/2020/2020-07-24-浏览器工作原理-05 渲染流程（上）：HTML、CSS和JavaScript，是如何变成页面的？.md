具体笔记在 04 章节：#30 

![](https://raw.githubusercontent.com/Daotin/pic/master/img/20190912173635.png)



## 思考题

*如果下载 CSS 文件阻塞了，会阻塞 DOM 树的合成吗？会阻塞页面的显示吗？*



不会阻塞DOM树合成，DOM树的合成只需要html文件就够了，但是会阻塞页面的显示，因为需要css进行样式计算和布局，没有这些无法进行页面的渲染，进而阻塞页面的显示。
