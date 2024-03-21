# 2024.3.22 重磅发布本地化部署！！！
# 本仓库为业务版用户部署到本地的专用服务。（可防止 saas 服务波动问题，解决异地登录，下载流量限制等）
# 部署本地还需定时两个月更新，务必 Star，防止走丢！


#### 介绍
wechat-self是E云API业务私有部署产品，当用户使用E云API需要频繁调用下载类接口、考虑数据安全问题或为了防止Saas环境波动等情况时可使用wechat-self实现私有化部署，此项目是通过Docker容器部署，部署项目前服务器需先安装Docker。

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
mkdir -p /root/media
docker run -itd -v /root/media:/root/wechat-ee/wechat/media --net=host --privileged=true --name=wechat-self wechat-self /usr/sbin/init
```
3、将容器设置成开机运行

```
docker update --restart=always wechat-self
```
#### 使用教程
- 首次使用需要同步E云账号，参数请按照实际填写
  - baseUrl: E云平台的地址
  - userName: E云平台账号
  - password: E云平台密码 
  ```shell
  curl --location --request POST 'http://服务器ip:9899/sync/account' \
  --header 'Content-Type: application/json' \
  -d '{
      "password": "111111",
      "userName": "188888888",
      "baseUrl": "http://110.120.119.0:9899"
  }'
  ```
- 服务调用地址：http://服务器ip:9899
- 文件/图片下载会返回文件相对路径，在文件地址前拼接此地址即可访问：http://服务器ip:9002/media/
- [api接口地址（点击）](https://www.wkteam.cn/api-wen-dang2/)

#### 注意事项

由于容器和linux系统端口共用，建议linux系统不要安装和运行其他服务导致端口冲突
