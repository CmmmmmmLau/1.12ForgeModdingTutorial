---
2020.7.19 重新编写
---


Minecraft 1.12 Forge Mod开发学习笔记学习笔记习笔记习笔记学习笔记学习笔记习笔记笔记记记



这是一篇用来记录本人在学习MC模组开发的过程,以及中途遇到各种问题的时候的解决方法和思路.‌

因为国内在1.12版本的模组开发教程资源较少,所以学习教程都来源于国外教程,并且在多个教程同时参考比对的情况下学习,并且包含视频教程.特此开此文章用来记录.该日志以文档的形式记录,因为可以不废网站排版以及方便做导航.‌\
~~事实证明这样了还能防止因为忘了续期Blog的服务器而丢档.~~

而学习的过程则是按照ustc-zzzz的教程的步骤来.只不过用其他教程资料.‌

因为旧的文档写的过于繁琐复杂且不便查看.\
决定重新写一个文档.[旧档备份](http://old.cmmmmmm.com)

**使用到的国外教程:**‌

* [_**shadowfacts**_](https://shadowfacts.net/tutorials/forge-modding-112/) (主)
* [**suppergerrie2**](https://suppergerrie2.com/category/forge-tutorial/forge-tutorial-1-12/page/2/)(主)
* [**Harry Talks**](https://www.youtube.com/watch?v=Hhzsc9R446A\&list=PLiDUvCGH5WEUEV9nc0Ll2pzUFmSFc21uR\&index=1)(视频教程)
* [_**cubicoder**_](https://cubicoder.github.io/tutorials/1-12-2/tutorials/) (辅.因内容较少)
* [_**Forge Doc**_](https://mcforge.readthedocs.io/en/latest/) (英) [_**Forge Doc**_](https://mcforge-cn.readthedocs.io/zh/latest/) (中)

‌

**使用到的国内教程:**‌

* [_**Harbinger**_](https://harbinger.covertdragon.team/) (严格来讲这个不算教程而是参考书)
* [_**ustc-zzzz**_](https://fmltutor.ustc-zzzz.net/1.1-%E9%85%8D%E7%BD%AE%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E7%8E%AF%E5%A2%83.html) (1.8.9的教程,不到万不得已不看)
* [_**我的世界开发者中文指南**_](https://github.com/Mouse0w0/MinecraftDeveloperGuide) (以上讲到内容的总和,但包括其他与MC有关的开发)

‌

**使用的文档**:‌

* [Forge 14.23.5.2838 JavaDoc](https://forge.galaxy.nctu.me/forge/javadoc/14.23.5.2838-stable\_39/overview-summary.html)

‌

**使用的开发环境:**‌

* JDK1.8.0\_201
* IntelliJ IDEA Community Edition 2019.3.1 x64
* Minecraft 1.12.2
* Forge 14.23.5.2854
* MCP stable\_39
