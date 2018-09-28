# 安装
## JAVA环境
检查是否安装JDK8及以上版本`java -version`
若没有
yum安装java
```
yum list | grep java*
yum install java-1.8.0-openjdk.x86_64
```

## 安装logstash
```
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```
在` /etc/yum.repos.d/`创建`logstash.repo`
内容为
```
[logstash-6.x]
name=Elastic repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```
安装
`yum install logstash`

## 运行logstash
修改环境变量
`export PATH=/usr/share/logstash/bin:$PATH`

测试
`logstash -e ''`
