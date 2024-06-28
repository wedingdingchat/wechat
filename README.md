# wechat-self

#### 介绍
wechat-self是易云api私有化部署产品，当用户使用易云api出现风控或有大量微信号需要托管，云服务已无法满足用户需求时可使用wechat-self实现私有化部署，此项目是通过docker容器部署，部署项目前服务器需先安装docker。

#### 软件架构
wecaht-self是通过go语言和java编写，使用mysql+redis作为后台数据存储


#### centos docker安装(已安装docker可跳过)  


1、安装gcc相关
```
yum -y install gcc
yum -y install gcc-c++
```
2、配置镜像

```
yum install -y yum-utils
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum makecache fast
```
3、安装docker

```
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
4、启动docker

```
systemctl start docker
//将docker设置成开机自启动
systemctl enable docker.service
```
#### 下载运行wechat-self

1、导入镜像

```
 docker pull registry.cn-hangzhou.aliyuncs.com/wechat-self/wechat-self:latest
 
 docker tag registry.cn-hangzhou.aliyuncs.com/wechat-self/wechat-self wechat-self
```
2、运行镜像容器

```
docker run -itd --net=host --privileged=true --name=wechat-self wechat-self /usr/sbin/init
```
3、将容器设置成开机运行

```
docker update --restart=always wechat-self
```
#### 使用教程
1、服务调用地址：http://服务器ip:9899  
2、文件/图片下载地址：http://服务器ip:9002  
3、[api接口地址（点击）](https://www.wkteam.cn/api-wen-dang2/)

#### 更新流程

```
1、选择更新版本下载
2、将文件解压至服务器root目录
3、执行命令
    chmod +x install-wechat.sh
    ./install-wechat.sh
```
#### 更新日志（点击下载）
#### [2024-06-28](https://pan.xunlei.com/s/VO0TwbsKVne-YWfbj2MsQeTVA1?pwd=9tpj#) 

#### 注意事项

由于容器和linux系统端口共用，建议linux系统不要安装和运行其他服务导致端口冲突
