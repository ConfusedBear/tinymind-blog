---
title: 本地部署 deepseek-r1
date: 2025-02-08T06:48:56.858Z
---


以Mac系统为例，需完成以下内容
1. 本地使用 ollama 运行 deepseek-r1 以支持本地 API 调用功能
2. 本地使用 open-webui 配置并调用 ollama 以支持可视化界面

# 1. 支持本地 API 调用
## 1.1 安装 ollama
登录 https://ollama.com/download 以下载 ollama，安装后使用命令行进行操作
## 1.2 安装 deepseek-r1
进入 ollama 的 deepseek-r1 专区 https://ollama.com/library/deepseek-r1 

并选择合适参数值的模型
1. 1.5B 模型最小，仅 1G 左右，但效果不太好
2. 7B 模型适中，5G 左右，在M系列芯片 MacBook 有流畅的体验
3. 32B 模型偏大，20G 左右，在M系列芯片 MacBook 运行较为卡顿
3. 671B 模型最大，400G 左右，完全体仅可在专用卡上运行

本地打开终端，输入`ollama run deepseek-r1:7B`即可自动下载对应模型
## 1.3 本地 API 调用
本地打开终端
1. 输入`ollama run deepseek-r1`以终端形式运行此模型，可直接在终端输入请求内容
2. 输入`ollama serve`以服务端形式运行此模型，此时可通过发起HTTP请求输入请求内容，如
```
curl --location 'http://127.0.0.1:11434/api/generate' \
--header 'Content-Type: application/json' \
--data '{
    "model": "deepseek-r1",
    "prompt": "你是谁?"
}'
```
若功能正常则开始下一阶段

# 2. 配置可视化界面
## 2.1 下载 docker
打开 https://www.docker.com/products/docker-desktop/ 选择对应平台 app 下载
## 2.2 安装 open-webui
open-webui GitHub：https://github.com/open-webui/open-webui
open-webui QuickStart：https://docs.openwebui.com/getting-started/quick-start/
打开 docker，打开终端，输入命令：
1. `docker pull ghcr.io/open-webui/open-webui:main` 此命令为拉取镜像，因为 mac 环境无英伟达 GPU，因此最后版本号只能选择`main`，若环境有英伟达显卡，则版本号可换为`cuda`
2. `docker run -d -p 3000:8080 -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://host.docker.internal:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:main` 此命令为本地启动 open-webui，且映射容器内 8080 端口到宿主机 3000端口，并配置 ollama 地址为 http://host.docker.internal:11434
> 对于 Mac / Windows 环境，由于 Docker 使用了虚拟化技术，并不是直接在宿主机上运行，因此如果要打通 docker 容器和宿主机的 ollama 服务，则只能使用 host.docker.internal 域名，此域名为 docker 单独为虚拟化环境下的特殊 DNS 名称，指向宿主机的 IP 地址，用于让容器访问宿主机的服务
> 对于 Linux 环境，由于 Docker 就在宿主机上运行，因此无需且无法使用 host.docker.internal 域名，因此启动命令需要变更为 `docker run -d -p 3000:8080 --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:main` 即增加了` --network=host` 并将 `host.docker.internal` 替换为了 `127.0.0.1`
## 2.3 open-webui 使用
