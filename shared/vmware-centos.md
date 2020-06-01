# windows 使用 vmware 安装 centos 虚拟机

## 依赖

vmware 15, CentOS-7-x86_64-DVD-1908

## 安装步骤

参考[文章](https://blog.csdn.net/The_Reader/article/details/84446868)

## 问题

1. `ifconfig` 查看 ip 报错 `ifconfig command not found`， 原因没有安装对应包， 使用 `yum install net-tools` 安装包。

2. `yum install net-tools` 报错 `cannot find a valid baseurl`。 参考[解决方法](https://www.glbwl.com/centos-7-repo-base.html)。