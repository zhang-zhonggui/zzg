---
title: jsdelivr国内加速节点
date: 2024-06-17 16:21:28
tags: [jsdelivr,cdn]
---

# jsdelivr npm CDN 国内加速节点

<!--more-->

## JSDelivr 介绍

JSDelivr 是由 **@Cloudflare** 提供的免费开源公共 CDN。

### 默认节点

- 默认提供的节点是：**cdn.jsdelivr.net**
  该节点国内几乎不可用，需要使用可用性高的节点作为替代。

### 可用的 jsDelivr 节点

- 常用于加速 GitHub/npm 项目，可通过更改节点改善项目在国内的可用性。

| 节点                      | 描述            | 可用性     |
| ------------------------- | --------------- | ---------- |
| gcore.jsdelivr.net        | Gcore 节点      | 可用性高   |
| testingcf.jsdelivr.net    | Cloudflare 节点 | 可用性高   |
| quantil.jsdelivr.net      | Quantil 节点    | 可用性一般 |
| fastly.jsdelivr.net       | Fastly 节点     | 可用性一般 |
| originfastly.jsdelivr.net | Fastly 节点     | 可用性低   |
| test1.jsdelivr.net        | Cloudflare 节点 | 可用性低   |
| cdn.jsdelivr.net          | 通用节点        | 可用性低   |

### 第三方提供的 jsDelivr 节点

- 以下是一些第三方提供的 jsDelivr 节点，可用于国内访问。

| 节点               | 来源    | 特点                                            |
| ------------------ | ------- | ----------------------------------------------- |
| jsd.cdn.zzko.cn    | 国内CDN | 目前正在使用                                    |
| jsd.onmicrosoft.cn | 国内CDN | 该项目所有域名停止运行维护(全线切入CloudFlare), |
| jsdelivr.b-cdn.net | 台湾CDN | 没用过不做评价                                  |
| cdn.jsdelivr.us    | Anycast | 没用过不做评价                                  |

请根据项目需求和国内访问情况选择合适的 CDN 节点。

**推荐  Cloudflare 节点与  Gcore 节点 或者 jsd.cdn.zzko.cn**

文章来自[酿俗boi](https://www.cnblogs.com/xkboi/p/18218418)
