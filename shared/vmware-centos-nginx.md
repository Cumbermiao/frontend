# windows 使用 vmware 安装 centos 虚拟机 部署前端代码
## 依赖
vmware 15, CentOS-7-x86_64-DVD-2003, nginx-1.17.2 
## 1.vmware 15

### 下载地址:
<https://www.vmware.com/cn/products/workstation-player/workstation-player-evaluation.html>

### 安装参考：
<https://jingyan.baidu.com/article/9f63fb91fc896c88400f0ea6.html>

#### 总结：
> 1. 去VMware官网下载VMware Workstation 15 Player软件，完成下载后，双击安装包打开安装程序 
> 2. 出现VMware Workstation 15 Player安装向导。点击【下一步】继续
> 3. 然后勾上“我接受许可协议中的条款”后，点击【下一步】继续
> 4. 根据提示选择后点击下一步继续
> 5. 提示“安装向导已完成”，点击【完成】
> 6. 提示“必须重新启动系统”，点击【是】完成重启

## 2.CentOS-7
### 获取镜像:
<http://mirrors.aliyun.com/centos/7.8.2003/isos/x86_64/>

### 虚拟机配置
#### 参考：
<https://jingyan.baidu.com/article/546ae1854cb6185149f28c87.html>

#### 总结：
> 1. 去CentOS官网下载CentOS-7-x86_64-DVD-2003.iso，保存本地 
> 2. 打开VMware Workstation 15 Player程序
> 3. 点击【创建新虚拟机】
> 4. 选择想安装的操作系统iso文件所在路径
> 5. 输入【个性化Linux】的相关信息(用户名/全名/密码)
> 6. 输入虚拟机名称和虚拟机文件存放路径
> 7. 设置“最大磁盘大小”为所需的大小
> 8. 显示虚拟机相关的配置信息，点击【完成】开始安装虚拟机

#### 注意事项：
> 1. 指定磁盘容量时   选择“将虚拟磁盘存储为单个文件”   因为选择拆分为多个文件，虽可以更轻松的在计算机之间移动虚拟机，但可能会降低大容量磁盘的性能
> 2. 设置网络连接类型  选择“使用桥接网络”，各位安装虚拟机过程根据需要自行设置，这里补充说明如下：
>>   1. 使用桥接网络：虚拟机ip与本机在同一网段，本机与虚拟机可以通过ip互通，本机联网状态下虚拟机即可联网，同时虚拟机与本网段内其他主机可以互通，这种模式常用于服务器环境架构中。
>>    2. 使用网络地址转换（NAT）：虚拟机可以联网，与本机互通，与本机网段内其他主机不通。
>>    3. 使用仅主机模式网络：虚拟机不能联网，与本机互通，与本机网段内其他主机不通。

### 系统安装：
#### 参考：
<https://www.cnblogs.com/wcwen1990/p/7630545.html>

#### 总结：
> 1. 虚拟机控制台出现界面，选择Install CentOS liunx 7，点击回车键继续
> 2. 语言选择界面默认选择English，点击Continue继续
> 3. CentOS7安装配置，点击“installation destination”，进入如下界面，选中80g硬盘，下来滚动条到最后，选中“i will configure partitioning”，即自定义磁盘分区，最后点击左上角done进行磁盘分区规划：
> 4. 完成磁盘规划后，点击“NETWORK && HOST NAME”，修改操作系统主机名，然后点击done完成主机名配置，返回主配置界面
> 5. 这时发现右下角“begin installtion”按钮已经从原本的灰色变成蓝色，这说明已经可以进行操作系统安装工作了，点击“begin installtion”进行操作系统安装过程
> 6. 点击“root password”，修改root用户密码，如果密码安全度不高，当root密码设置成功再次返回安装界面时我们可以发现之前user setting界面红色警告消失了
> 7. 点击reboot重启操作系统

#### 注意事项：
> 1. 系统重启后，进入命令行界面，用ifconfig命令检查网卡是否启用以及是否获取IP地址，如果没有启用则需要手动进入配置文件/etc/sysconfig/network-script/ifcfg-ens32中，把ONBOOT=no改为yes即可。


## 3.XShell
### 下载地址
<https://pan.baidu.com/s/1EI3ADscMDQhxJlCgGD5YDA> 
密码：w2lv

## 4.nginx安装与配置
### 参考
<https://www.runoob.com/linux/nginx-install-setup.html>

### 总结：
> 1. nginx是C写的，需要用GCC编译          yum install gcc-c++ 
> 2. nginx中的rewrite module需要PCRE     yum install -y pcre pcre-devel
> 3. nginx中的gzip module需要zlib        yum install -y zlib zlib-devel
> 4. nginx中的HTTP SSL module需要OpenSSL yum install -y openssl openssl-devel
> 5. 下载nginx(最好不要放在根目录下)       wget http://nginx.org/download/nginx-1.17.2.tar.gz
> 6. 解压缩源码包并进入
>>    1. 解压缩                tar -zxvf nginx-1.17.2.tar.gz
>>    2. 进入解压缩后文件夹     cd nginx-1.17.2
> 7. 编译并安装                 ./configure
                               make && make install 
> 8. 配置nginx.config
> 9. 启动nginx
>>  1. 进入安装目录            cd /usr/local/nginx/sbin/  
>>   2. 启动                   ./nginx     

## 代码上传
当Nginx安装成功后，我们便可以把代码部署到Nginx中，选择代码需放置的文件夹位置，默认为“/opt/dtcloud/applications/intelligentPlatformCloud/platform-fe”，执行rz上传代码包，以zip压缩包为例，执行 unzip XX.zip命令进行解压缩（此处XX为压缩文件名，每种压缩文件类型的解压缩命令都不一样），如果文件名与此不一致，可执行mv A B命令进行重命名（A为目前文件夹名 B为目标文件名）。如需多次上传同样文件名的文件，需将原来的文件删除，删除文件命令：rm –f A（A为文件名）；删除文件夹以及文件夹中的文件命令：rm –rf B（B为文件夹名）
### 总结
> 1. rz                代码上传
> 2. unzip XX.zip      解压zip格式的压缩文件
> 3. rm -f XXX         删除XXX文件
> 4. rm -rf XXX        删除XXX文件夹
> 5. mv A B            将A文件重命名为B文件

### 配置
当Nginx安装完成，并且代码上传之后，我们需要根据要求进行Nginx配置，进入Ngnix的配置文件目录（/usr/local/nginx/conf），对Nginx进行配置
> 1. 配置用户或组，使得启动用户和Nginx工作用户一致，不然可能会导致页面403的问题
> 2. 配置端口号，默认8907,服务器地址
> 3. 默认目录为/opt/dtcloud/applications/intelligentPlatformCloud/platform-fe，部署项目名称即为platform-fe, 所以修改 server 下的 location root根目录的值。目录路径不正确会导致页面404.（既代码所在根目录）
> 4. 转发至后端的网关端口 正常是 8180
配置完成后，执行：wq 保存退出，
可使用/usr/local/nginx/sbin/nginx –t命令检查配置文件是否正确;如果没有问题可到安装目录（/usr/local/nginx/sbin）执行nginx启动命令：./nginx 如果刚刚已执行启动命令，此处应该执行./nginx –s reload重启命令。对配置文件的每次操作都必须执行重启命令。


### 注意事项
> 1. make 命令出现："make:*** No targets specified and no makefile found.Stop."参考<https://blog.csdn.net/shun35/article/details/94576800>
> 2. user 启动用户和nginx工作用户需一致,不然会导致nginx 403
> 3. 检查配置文件nginx.conf的正确性命令   /usr/local/nginx/sbin/nginx -t
> 4. 如果启动过程中出现端口占用的情况，可使用ps -ef | grep nginx查看进程占用的id，并且使用kill -9 processId 强制杀掉进程
> 5. 启动nginx时403的常见情况分析参考<https://blog.csdn.net/qq_35843543/article/details/81561240> 
> 6. 以下包含了 Nginx 常用的几个命令
> 进入安装目录（/usr/local/nginx/sbin），可执行nginx启动关闭命令。
>>   1. ./nginx –t                       #检查配置文件是否正确
>>   2. ps –ef | grep nginx              #查看nginx是否启动
>>   3. ./nginx                          #启动nginx
>>   4. ./nginx –s stop 或 pkill nginx   #停止nginx
>>   5. ./nginx –s quit                  #退出，等程序执行关闭后，建议使用此命令
>>   6. ./nginx -s reload                # 重动态加载配置文件，可以在不关闭nginx的情况下更新配置文件，使其生效，必须执行重启命令
>>   7. kill -9 processId                #如果启动过程中出现端口占用的情况，可使用ps -ef | grep nginx查看进程占用的id，并且使用 强制杀掉进程 


## 5.补充内容
* 基本上 vi/vim 共分为三种模式，分别是命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）
    * 命令模式: 用户刚刚启动 vi/vim，便进入了命令模式 
    * 输入模式：i 切换到输入模式，以输入字符。
    * 底线命令模式: :q 退出程序   :w 保存文件  :q! 不保存退出  按ESC键可随时退出底线命令模式
* yum install            安装
* ls                     列出根目录(\)下的所有目录 
* ifconfig               用于显示或设置网络设备 
* chmod -R 777 fileName  对目前目录下的所有文件与子目录进行相同的权限变更 
* mkdir AAA              在工作目录下，建立一个名为 AAA 的子目录
* rz                     上传文件
* ps -ef | grep nginx    显示所有进程信息
* wget XXXX              下载文件
* mv ABC /XXX/XXX        将ABC文件移动到该文件夹下
* linux下解压命令大全     <https://blog.csdn.net/kasmile/article/details/4369065?ops_request_misc=&request_id=&biz_id=102&utm_term=liunx%E8%A7%A3%E5%8E%8B%E5%91%BD%E4%BB%A4&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-2-4369065>