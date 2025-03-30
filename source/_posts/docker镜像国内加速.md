---
title: docker镜像国内加速
date: 2024-06-08 11:38:31
tags: docker
---

# docker镜像国内加速

<!--more-->

相关政策原因目前国内镜像加速几乎全部现在限制没了,只能用国内**大厂的库存**,或者**自行想办法**;在或者去**可信镜像中心**

# ~~1.目前docker镜像国内无法正常访问所以需要手动配置一下docker镜像加速~~

## 配置镜像加速

```txt
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://6gen6194.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 汇总国内可用镜像

### 可信镜像中心

加速地址：https://atomhub.openatom.cn/

支持：Docker Hub

限制：目前支持的镜像比较少,且版本比较老,网络不稳定

### ~~阿里云~~

加速地址：https://<6gen6194>.mirror.aliyuncs.com

支持：Docker Hub

限制：需要登录账号获取CODE

### ~~网易云~~

加速地址：[https://hub-mirror.c.163.com](https://link.zhihu.com/?target=https%3A//hub-mirror.c.163.com)

支持：Docker Hub

对外免费：是

### ~~百度云~~

加速地址：[https://mirror.baidubce.com](https://link.zhihu.com/?target=https%3A//mirror.baidubce.com)

支持：Docker Hub

对外免费：是

### ~~DaoCloud 镜像站~~

加速地址：[https://docker.m.daocloud.io](https://link.zhihu.com/?target=https%3A//docker.m.daocloud.io)

支持：Docker Hub、GCR、K8S、GHCR、Quay、NVCR 等

对外免费：是

### ~~Docker 镜像代理~~

加速地址：[https://dockerproxy.com](https://link.zhihu.com/?target=https%3A//dockerproxy.com)

支持：Docker Hub、GCR、K8S、GHCR

对外免费：是

### ~~南京大学镜像站~~

加速地址：[https://docker.nju.edu.cn](https://link.zhihu.com/?target=https%3A//docker.nju.edu.cn)

支持：Docker Hub、GCR、GHCR、Quay、NVCR 等

对外免费：是

### ~~上海交大镜像站~~

加速地址：[https://docker.mirrors.sjtug.sjtu.edu.cn/](https://link.zhihu.com/?target=https%3A//docker.mirrors.sjtug.sjtu.edu.cn/)

支持：Docker Hub、GCR 等

限制：无

### ~~科大镜像站~~

加速地址：[https://docker.mirrors.ustc.edu.cn](https://link.zhihu.com/?target=https%3A//docker.mirrors.ustc.edu.cn)

支持：Docker Hub、GCR、Quay

限制：仅供内部访问

### ~~Azure中国镜像~~

加速地址：[https://dockerhub.azk8s.cn](https://link.zhihu.com/?target=https%3A//dockerhub.azk8s.cn)

支持：Docker Hub、GCR、Quay

限制：仅供内部访问

### ~~七牛云~~

加速地址：[https://reg-mirror.qiniu.com](https://link.zhihu.com/?target=https%3A//reg-mirror.qiniu.com)

支持：Docker Hub、GCR、Quay

限制：已关闭

### ~~Docker 中国官方镜像~~

加速地址：[https://registry.docker-cn.com](https://link.zhihu.com/?target=https%3A//registry.docker-cn.com)

支持：Docker Hub

限制：已关闭

![image-20240608120530099](https://cdn.jsdelivr.us/gh/zhang-zhonggui/blog/bolg/image-20240608120530099.png)





