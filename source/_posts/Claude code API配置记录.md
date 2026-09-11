---
title: Claude code for VScode配置记录
author: 牧丛
date: 2026/1/19 10:00
index_img: /cover/cover8.jpg
categories: AI工具
tags:
  - Claude codes
  - 流程记录
excerpt: （想不出写什么摘要了）
math: true
toc: true
---

本文的主要内容都是参考[Xiaomi MiMo 开放平台](https://platform.xiaomimimo.com/#/docs/integration/claude-code)会在其中加入一些自己的理解，仅供参考。

Claude code采取的是MiMo-V2-Flash模型，它具备良好的代码理解与编程能力，兼容 Anthropic 接口协议，适用于代码助手、Agent 工具等多种场景。

## 获取 API Key

访问 [控制台-API Keys](https://platform.xiaomimimo.com/#/console/api-keys)，点击“新建 API key”按钮，输入名称以创建新的 API Key。

## 在 Claude Code CLI 中使用

### 配置环境

Linux和Macos系统的默认环境就是没有问题的

Windows系统主要是两条路径，一条是安装WSL，可以参考 [Windows 系统安装 WSL](https://learn.microsoft.com/en-us/windows/wsl/install)，还有一条路径是安装 Git for Windows，安装方法参考 [Windows 系统安装 Git for Windows](https://git-scm.com/install/windows) ，然后在WSL或者Git Bash中执行一下操作。

```bash
npm install -g @anthropic-ai/claude-code
```

注意，如果你之前没有安装过npm，需要先安装，WSL的话是直接在终端下载，Git Bash是在node官网下载，都很简单

### 配置 MiMo API Key

1. 在`~/.claude.json` 中，加入设置 `"hasCompletedOnboarding": true`，以跳过登录步骤。

2. 编辑或创建 Claude Code 的配置文件，路径为 `~/.claude/settings.json`，在该文件中添加或更新 `env` 字段，需要将 `$MIMO_API_KEY` 替换为从 [控制台-API Keys](https://platform.xiaomimimo.com/#/console/api-keys) 获取的 API Key。

   ```json
   {
     "env": {
       "ANTHROPIC_BASE_URL": "https://api.xiaomimimo.com/anthropic",
       "ANTHROPIC_AUTH_TOKEN": "$MIMO_API_KEY",
       "ANTHROPIC_DEFAULT_OPUS_MODEL": "mimo-v2-flash",
       "ANTHROPIC_DEFAULT_SONNET_MODEL": "mimo-v2-flash",
       "ANTHROPIC_DEFAULT_HAIKU_MODEL": "mimo-v2-flash"
     }
   }
   ```

$MIMO-API-KEY就是上面你生成的API Keys

### 在WSL或者Git Bash中使用

上述配置完成后，WSL或者Git Bash中输入`Claude`，即可进入模式：

```bash
claude
```

如果成功应该会显示以下图片：

![](../../themes/fluid/source/picture/Claude%20code1.jpg)

ENTER之后显示：

![](../../themes/fluid/source/picture/Claude%20code2.jpg)

这个时候就基本成功了

## 在 Claude Code for VS Code 插件中使用

直接在VScode中搜索“Claude Code for VS Code”插件，下载完成之后在VScode左上角会出现橙色标志：

![](../../themes/fluid/source/picture/Claude%20code3.jpg)

点击这个橙色标志，就会出现下图情况：

![](../../themes/fluid/source/picture/CLaude%20code4.jpg)

这个时候你问它一些问题，它就可以正常回答了。

注意，如果你是Windows系统，而且VS code是在Windows里面的，必须通过Git for windows的安装方法，才能完成。WSL的方法是不行的。

最后提醒大家，笔者这里使用的小米key API的免费截止时间是在2025年1月20日，对，笔者写完这篇Blog的时候它只剩下不到2天的免费时间了，已经没有太强的实际意义了。笔者就是一个纯joker，不过，这个过程还是希望能够给到读者一些启发（大概率也不会有），也算是笔者自己的一个记录，谢谢！
