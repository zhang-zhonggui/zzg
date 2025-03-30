---
title: centos7安装jdk17
date: 2023-09-14 14:50:36
tags: [centos,jdk]
---

# centos7安装jdk17

<!--more-->

### 1.首先下载yum 进行更新

```shell
yum  update -y
```

#### 2.下载wget,vim

```shell
yum install -y wget vim
```

#### 3.下载jdk

```shell

# 在 /usr/local 目录下安装jdk
cd /usr/local
wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz
tar -zxvf jdk-17_linux-x64_bin.tar.gz 
# 将jdk-17改名为java
mv jdk-17 java

```

#### 4.添加环境变量

```shell
# 进入profile文件，按i进入编辑模式
vim /etc/profile
# 在文件最下方添加
export JAVA_HOME=/usr/local/java
export PATH=$PATH:$JAVA_HOME/bin;
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar;
# 按下Esc退出编辑模式
# 下一步按住shift 再按俩次 z 键，保存配置文件信息
# 重新加载环境变量
source /etc/profile
# 
cd /
java -version

```

#### 5.查询是否安装成功

```shell
java -version
```

