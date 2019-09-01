---
layout: post
title: Linux 命令速查
excerpt: 记录一些有时会忘记的小命令
date: 2018-09-20 15:08:05
categories: Linux
tags: Linux 
author: Garaguru
mathjax: true
---

* content
{:toc}

## 寻找一个文件
`find . -name "*.c"`


## 更改默认编辑器
`sudo update-alternatives --config editor`

```
  0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
* 4            /usr/bin/vim.tiny    15        manual mode
```
## 设置环境变量
当前 shell 内：
```
export -p //列出当前的环境变量值
export MYENV=7 //定义环境变量并赋值
export PATH=/usr/local/webserver/mysql/bin:$PATH // 配置PATH变量
```

永久：
```
vim /etc/profile // 打开配置文件
export PATH="/usr/local/webserver/mysql/bin:$PATH" // 在 profile 中最后一行添加命令

source /etc/profile // 保存后运行这条命令即可（即在当前shell内运行 profile 里的命令），也可以以 . /etc/profile （. 与 /etc/profile 之间有一个空格，等价于 source /etc/profile
```

## 查看磁盘占用情况
```
du -h . # 分析当前目录磁盘占用， -h 是把字节数转化为 KB MB GB 等
```

## 压缩、解压文件
```
touch a.c # 创建一个空文件
tar -czvf test.tar.gz a.c   # 压缩 a.c文件为test.tar.gz

tar -tzvf test.tar.gz       # 列出压缩文件内容

tar -xzvf test.tar.gz      # 解压文件
```

## http 下载
```
wget [url]
```