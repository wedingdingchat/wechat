# 2024.3.22 重磅发布本地化部署！！！
# 本仓库为业务版用户部署到本地的专用服务。（可防止 saas 服务波动问题，解决异地登录，下载流量限制等）
# 部署本地还需定时两个月更新，务必 Star，防止走丢！


#### 介绍
wechat-self是E云api本地化部署产品，当用户需求频繁下载、批量下载，云服务带宽响应速度已无法满足用户需求时，可使用wechat-self实现本地化部署，此项目是通过docker容器部署，部署项目前服务器需先安装docker。


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
## 初次使用
#### 下载运行wechat-self

1、导入镜像

```
 docker pull registry.cn-hangzhou.aliyuncs.com/wechat-self/wechat-self:latest
 
 docker tag registry.cn-hangzhou.aliyuncs.com/wechat-self/wechat-self wechat-self
```
2、运行镜像容器

```
mkdir -p /root/media
docker run -itd -v /root/media:/root/wechat-ee/wechat/media -p 9002:9002 -p 9899:9899  --privileged=true --name=wechat-self wechat-self /usr/sbin/init
```
3、将容器设置成开机运行

```
docker update --restart=always wechat-self
```
#### 使用教程
- 首次使用需要在服务器执行curl命令同步E云账号，参数请按照实际填写
  - baseUrl: E云平台的BaseUrl
  - userName: E云平台账号
  - password: E云平台密码 
  ```shell
  curl --location --request POST 'http://127.0.0.1:9899/sync/account' \
  --header 'Content-Type: application/json' \
  -d '{
      "userName": "E云平台账号",
      "password": "E云平台密码",
      "baseUrl":  "E云平台BaseUrl" 
  }'
  ```
- 服务调用地址：http://服务器ip:9899
- 文件/图片下载会返回文件相对路径，在文件地址前拼接此地址即可访问：http://服务器ip:9002/media/
- [api接口地址（点击）](https://www.wkteam.cn/api-wen-dang2/)


## 后续有新版本如何更新？
#### 更新流程

```
本地化更新流程

1、导出本地化微信设备信息
http://本地化服务ip:9899/exportWxInfo
{
    "wids":["wxid***6doh22"] //wcId
}

2、删除镜像重新下载镜像

3、导入微信设备信息
http://本地化服务ip:9899/importWxInfo
{
    "data":"1556389ACA3AC809EFAF74ADB03B31EECA6404B198812A312BC306DE77E3EC7AB135F91766EABF8FB6A6A444D....." //导出设备信息返回数据
}

本地化微信如何登录

1、通过平台或api在sass服务中登录微信

2、导出sass微信设备信息
http://sass服务ip:9899/exportWxInfo
{
    "wids":["wxid***6doh22"] //wcId
}

3、导入本地化微信设备信息
http://本地化服务ip:9899/importWxInfo
{
    "data":"1556389ACA3AC809EFAF74ADB03B31EECA6404B198812A312BC306DE77E3EC7AB135F91766EABF8FB6A6A444D....." //导出设备信息返回数据
}
```

#### 注意事项
- 本地化部署不支持扫码登录，需在sass登录成功后导出微信信息然后再导入到本地化服务中
- 由于容器和linux系统端口共用，建议linux系统不要安装和运行其他服务导致端口冲突
- 容器需要访问外网链接微信服务，服务器出口网络需要全部放开，否则会导致服务无法正常启动
- 扫码出现“在新设备完成验证以继续登录”deviceType传car重新取码登录，本地化版本不支持mac类型取码
