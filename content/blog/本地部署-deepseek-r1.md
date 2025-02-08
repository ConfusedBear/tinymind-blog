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
