---
title: AWS上架設Docker
date: 2019-05-25 13:44:28
categories:
- Docker
tags:
- Docker
- AWS
---

最近看到同事都在研究k8s, 但想想自己連Docker還沒用過，所以趁著假期來學習並記錄一下過程。如果不知道什麼是Docker, 那知自己上網查一下吧XD。 AWS EC2 的教學可以參考官方教學 [AWS 教學](https://aws.amazon.com/tw/getting-started/tutorials/launch-a-virtual-machine/)

在這里我部署一個載有nginx的container到Ubuntu 18.04 上. 

### 設置安全群組規則

1. 先在aws的安全群組（security group） 增加 inbound rules (port 80) 允許外來的連線

{%img /images/security_gp.png 600 400 %}


### 增加儲存倉，用於安裝及更新Docker


1. 更新apt packages
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo apt-get update
{% endcodeblock %}

2. 安裝 packages 
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg-agent \
software-properties-common
{% endcodeblock %}

3. 增加Docker官方GPG金鑰:
{% codeblock lang:shell line_number:false highlight:true%}
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
{% endcodeblock %}

4. 檢驗金鑰
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo apt-key fingerprint 0EBFCD88
    
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
{% endcodeblock %}

5. 建立倉庫
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
{% endcodeblock %}

### 安裝Docker

1. 更新 apt packages
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo apt-get update
{% endcodeblock %}

2. 安裝最新版本的Docker
{% codeblock lang:shell line_number:false highlight:true%}
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
{% endcodeblock %}

### 建立Nginx 環境

1. 下載 Nginx image
{% codeblock lang:shell line_number:false highlight:true%}
$ docker pull nginx
{% endcodeblock %}

2. 查看本地所有的docker image
{% codeblock lang:shell line_number:false highlight:true%}
$ docker images
{% endcodeblock %}

3. 啟動 nginx,命名為new_server, 並將docker container 80 port 對應到本機的80 port
{% codeblock lang:shell line_number:false highlight:true%}
$ docker run -d -p 80:80 --name new_server nginx
{% endcodeblock %}

4. 檢查 Docker container 是否運行
{% codeblock lang:shell line_number:false highlight:true%}
$ docker ps
{% endcodeblock %}

5. 往AWS的 提供的 public dns 就能看到 nginx 的主頁