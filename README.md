# 阿西吧の监控库
# 前言

此项目作为个人日常线报监控专用,纯收集,无审查,由于只在自己的青龙里使用，所以脚本不是完善，若有不足的地方请提出。

# 部署方法

## 容器化部署

在 Linux 服务器上部署，首先安装 docker (若已安装请忽略此步):

```shell
# 一键安装docker
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

# 查看docker版本
docker -v

# 设置开机启动
systemctl enable docker

# 启动
systemctl start docker
```

之后部署auto-proxy-pool代理池：

```shell
# 创建文件夹
mkdir -p /root/proxypool

# 进入文件夹
cd /root/proxypool

# 修改配置文件
vim proxy.yml
```
键盘按下i进入编辑模式,小白填写代理池api地址即可,写入最简配置(务必看注释):
##  [星空代理 (最低8R/1W个IP,签到星币免费兑IP)](http://www.xkdaili.com/?ic=6a9sesfq)
##  [携趣网络 (最低10R/2W个IP,每日免费送0.1W测试IP)](https://www.xiequ.cn/)
```shell
changeRequest:
  - hostRegex: (.+\.jd\.com)|(.+\.isvjd\.com)|(.+\.isvjcloud\.com) #正则表达式
    proxy: proxy
upstream:
  proxy: #自定义名字
    template: '{{$x := regexFindAll "\\d{1,3}(\\.\\d{1,3}){3}:\\d{2,5}" . -1}}{{range $s := $x}}{{printf "http://%s" $s}}{{end}}'
    apiUrl: #这里是填写代理池api地址,格式：txt,换行\n,提取1个(此行双引号后有一个空格 #号前要有一个空格)
    checkRetryNumber: 2 #检测代理次数，超过次数则获取新代理
    apiRetryNumber: 5 #上游重试次数，超过次数则采用直连
    checkUrl: http://jd.com/,http://baidu.com/,http://bilibili.com/ #检测代理是否有效，不用改
    lifecycle: -1 #自动失效时间，-1为不自动失效，单位秒
    maxSize: 2 #保留几个上游
    checkTimeout: 2s #单个check超时时间
    checkRetryWait: 500ms #重试check前等待时间
    requestInterval: 1000ms #请求时间间隔，在这个时间内只会请求一次api
```
键盘按下Esc然后键入:wq后Enter保存

```shell
# 重启auto-proxy-pool容器
docker restart auto-proxy-pool

# 查看auto-proxy-pool容器控制台输出
docker logs -f auto-proxy-pool
```
如果日志输出正常则表明代理池运行成功，每次修改代理池配置文件都要重启代理池才会生效哦！



## 已在每个脚本开头添加auto-proxy-pool代理池调用代码
## 部署auto-proxy-pool代理池项目[传送门](https://blog.qninq.cn/archives/proxypool.html)
## 如有问题请联系我 BY [Axiba](https://t.me/lyc_axiba)
ql repo https://github.com/LYC-axiba/JD-Monitoring-library.git "jd_" "" "^jd[^_]|USER|function|sendNotify|magic|h5sts"
