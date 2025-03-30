---
title: 修改host访问Google翻译
date: 2025-03-15 20:53:07
tags: 学习
---

# 修改host访问Google翻译

<!--more-->

目前国内使用Google翻译由于Google的种种原因不能使用了，所有需要进行host修改才能进行正常的访问！但最近的几个月Google应该是修改了策略，导致目前的ip是动态的！大概修改了过了一天就会失效，这里提供的一下国内可以长期使用的ip，ip的地址是指向阿里云的，大概是阿里内部的进行的代理吧，这里感谢哪些默默做代理的人员。

## host配置

```html
121.43.186.252 translate.google.com
121.43.186.252 translate.googleapis.com
121.43.186.252 translate-pa.googleapis.com
121.43.186.252 jnn-pa.googleapis.com
```

## 其他地区可用ip[^1]

121.43.186.252 浙江 杭州 

121.43.177.34   浙江 杭州

47.103.34.63     上海 上海

47.103.46.164   上海 上海

47.113.110.152 广东 深圳

120.79.149.13   广东 深圳

39.101.73.57      北京 北京

<font color=red>**注：本人使用了大概四个月目前无任何问题**</font>

## 如果所有都不可用，可用却[GoogleTranslateIpCheck](https://github.com/Ponderfly/GoogleTranslateIpCheck)查找

[^1]:https://github.com/Ponderfly/GoogleTranslateIpCheck/issues/71

