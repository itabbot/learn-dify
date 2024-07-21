# 在 Docker 中安装体验 Dify<!-- omit in toc -->

- [1. 网络畅通情况](#1-网络畅通情况)
  - [1.1. 环境准备](#11-环境准备)
  - [1.2. 安装](#12-安装)
  - [1.3. 访问](#13-访问)
- [2. 网络受限情况](#2-网络受限情况)
  - [2.1. 环境准备](#21-环境准备)
  - [2.2. 安装](#22-安装)
  - [2.3. 访问](#23-访问)

## 1. 网络畅通情况

### 1.1. 环境准备

```shell
# 使用全新的云主机（香港地区）
# 8核CPU 32GB内存
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04 LTS
Release:        24.04
Codename:       noble

# 安装Docker
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh

# 安装成功
$ docker -v
Docker version 27.0.3, build 7d4bcd8

# 系统自带GIT
$ git -v
git version 2.43.0
```

### 1.2. 安装

```shell
# 克隆 Dify 源代码至本地
$ git clone https://github.com/langgenius/dify.git
Cloning into 'dify'...
remote: Enumerating objects: 70690, done.
remote: Counting objects: 100% (554/554), done.
remote: Compressing objects: 100% (342/342), done.
remote: Total 70690 (delta 302), reused 438 (delta 210), pack-reused 70136
Receiving objects: 100% (70690/70690), 39.92 MiB | 13.40 MiB/s, done.
Resolving deltas: 100% (50603/50603), done.


# 进入 Dify 源代码的 docker 目录，执行一键启动命令
$ cd dify/docker
$ cp .env.example .env
$ sudo docker compose up -d
[+] Running 75/9
 ✔ ssrf_proxy Pulled                            68.1s
 ✔ api Pulled                                  114.8s
 ✔ web Pulled                                   31.8s
 ✔ db Pulled                                   117.1s
 ✔ weaviate Pulled                              83.2s
 ✔ sandbox Pulled                               17.0s
 ✔ worker Pulled                               114.8s
 ✔ nginx Pulled                                 62.0s
 ✔ redis Pulled                                 13.4s
[+] Running 11/11
 ✔ Network docker_default             Created    0.1s
 ✔ Network docker_ssrf_proxy_network  Created    0.0s
 ✔ Container docker-web-1             Started    5.5s
 ✔ Container docker-redis-1           Started    5.4s
 ✔ Container docker-weaviate-1        Started    5.4s
 ✔ Container docker-ssrf_proxy-1      Started    5.5s
 ✔ Container docker-db-1              Started    5.5s
 ✔ Container docker-sandbox-1         Started    5.4s
 ✔ Container docker-api-1             Started    0.8s
 ✔ Container docker-worker-1          Started    0.8s
 ✔ Container docker-nginx-1           Started    1.0s


# 检查是否所有容器都正常运行
$ sudo docker compose ps
NAME                  IMAGE                              COMMAND                  SERVICE      CREATED         STATUS                   PORTS
docker-api-1          langgenius/dify-api:0.6.14         "/bin/bash /entrypoi…"   api          2 minutes ago   Up 2 minutes             5001/tcp
docker-db-1           postgres:15-alpine                 "docker-entrypoint.s…"   db           2 minutes ago   Up 2 minutes (healthy)   5432/tcp
docker-nginx-1        nginx:latest                       "sh -c 'cp /docker-e…"   nginx        2 minutes ago   Up About a minute        0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp
docker-redis-1        redis:6-alpine                     "docker-entrypoint.s…"   redis        2 minutes ago   Up 2 minutes (healthy)   6379/tcp
docker-sandbox-1      langgenius/dify-sandbox:0.2.1      "/main"                  sandbox      2 minutes ago   Up 2 minutes
docker-ssrf_proxy-1   ubuntu/squid:latest                "sh -c 'cp /docker-e…"   ssrf_proxy   2 minutes ago   Up 2 minutes             3128/tcp
docker-weaviate-1     semitechnologies/weaviate:1.19.0   "/bin/weaviate --hos…"   weaviate     2 minutes ago   Up 2 minutes
docker-web-1          langgenius/dify-web:0.6.14         "/bin/sh ./entrypoin…"   web          2 minutes ago   Up 2 minutes             3000/tcp
docker-worker-1       langgenius/dify-api:0.6.14         "/bin/bash /entrypoi…"   worker       2 minutes ago   Up 2 minutes             5001/tcp
```

### 1.3. 访问

浏览器打开： `http://<主机名>`，云主机直接用公网 IP 访问即可。

## 2. 网络受限情况

### 2.1. 环境准备

```shell
# 使用全新的云主机（大陆地区）
# 2核CPU 4GB内存
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04 LTS
Release:        22.04
Codename:       jammy

# 安装Docker
# 由于网络问题，先从官网下载安装包文件，然后上传到云主机
# 下载地址（匹配上述操作系统）：https://download.docker.com/linux/ubuntu/dists/jammy/pool/stable/amd64/
$ sudo dpkg -i ./containerd.io_1.7.18-1_amd64.deb \
  ./docker-ce_27.0.3-1~ubuntu.22.04~jammy_amd64.deb \
  ./docker-ce-cli_27.0.3-1~ubuntu.22.04~jammy_amd64.deb \
  ./docker-buildx-plugin_0.15.1-1~ubuntu.22.04~jammy_amd64.deb \
  ./docker-compose-plugin_2.28.1-1~ubuntu.22.04~jammy_amd64.deb

# 安装成功
$ docker -v
Docker version 27.0.3, build 7d4bcd8

# 系统自带GIT
$ git --version
git version 2.34.1
```

修改 Docker 源：

```shell
# 修改Docker配置文件
$ sudo vim /etc/docker/daemon.json

# 添加内容
{
    "registry-mirrors": [
        "https://mirror.ccs.tencentyun.com",
        "https://docker.m.daocloud.io",
        "https://docker.nju.edu.cn",
        "https://dockerproxy.com"
    ]
}

# 重启Docker服务
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker

# 验证配置
$ sudo docker info
```

### 2.2. 安装

```shell
# 下载代码zip包：https://github.com/langgenius/dify
# 上传到服务器，并解压缩包
$ unzip dify-main.zip


# 进入 Dify 源代码的 docker 目录，执行一键启动命令
$ cd dify-main/docker
$ cp .env.example .env
$ sudo docker compose up -d
[+] Running 75/9
 ✔ db Pulled                                   30.4s
 ✔ weaviate Pulled                             15.1s
 ✔ worker Pulled                               83.6s
 ✔ sandbox Pulled                              35.6s
 ✔ web Pulled                                  42.0s
 ✔ redis Pulled                                 5.3s
 ✔ nginx Pulled                                27.0s
 ✔ ssrf_proxy Pulled                           40.7s
 ✔ api Pulled                                  83.6s
[+] Running 11/11
 ✔ Network docker_default             Created   0.1s
 ✔ Network docker_ssrf_proxy_network  Created   0.1s
 ✔ Container docker-weaviate-1        Started   1.7s
 ✔ Container docker-sandbox-1         Started   2.0s
 ✔ Container docker-db-1              Started   1.9s
 ✔ Container docker-ssrf_proxy-1      Started   1.9s
 ✔ Container docker-web-1             Started   1.9s
 ✔ Container docker-redis-1           Started   2.0s
 ✔ Container docker-worker-1          Started   2.9s
 ✔ Container docker-api-1             Started   2.9s
 ✔ Container docker-nginx-1           Started   3.9s


# 检查是否所有容器都正常运行
$ sudo docker compose ps
NAME                  IMAGE                              COMMAND                  SERVICE      CREATED              STATUS                        PORTS
docker-api-1          langgenius/dify-api:0.6.14         "/bin/bash /entrypoi…"   api          About a minute ago   Up About a minute             5001/tcp
docker-db-1           postgres:15-alpine                 "docker-entrypoint.s…"   db           About a minute ago   Up About a minute (healthy)   5432/tcp
docker-nginx-1        nginx:latest                       "sh -c 'cp /docker-e…"   nginx        About a minute ago   Up About a minute             0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp
docker-redis-1        redis:6-alpine                     "docker-entrypoint.s…"   redis        About a minute ago   Up About a minute (healthy)   6379/tcp
docker-sandbox-1      langgenius/dify-sandbox:0.2.1      "/main"                  sandbox      About a minute ago   Up About a minute
docker-ssrf_proxy-1   ubuntu/squid:latest                "sh -c 'cp /docker-e…"   ssrf_proxy   About a minute ago   Up About a minute             3128/tcp
docker-weaviate-1     semitechnologies/weaviate:1.19.0   "/bin/weaviate --hos…"   weaviate     About a minute ago   Up About a minute
docker-web-1          langgenius/dify-web:0.6.14         "/bin/sh ./entrypoin…"   web          About a minute ago   Up About a minute             3000/tcp
docker-worker-1       langgenius/dify-api:0.6.14         "/bin/bash /entrypoi…"   worker       About a minute ago   Up About a minute             5001/tcp
```

### 2.3. 访问

浏览器打开： `http://<主机名>`，云主机直接用公网 IP 访问即可。
