---
title: "建立自己的ChatGPT Discord機器人"
description: "ChatGPT Discord 機器人"
date: 2023-03-09T14:49:00+08:00
draft: false
tags: [ChatGPT Discord 機器人, ChatGPT, Discord 機器人, gpt-4, Gemini-Pro]
---

簡單在 Discord 架設你自己 AI 機器人教學

* 聊天支援 Google Gemini-Pro, OPENAI GPT-4
* 圖片生成支援 Bing, Google Gemini


> 開源在 [https://github.com/Zero6992/chatGPT-discord-bot](https://github.com/Zero6992/chatGPT-discord-bot)



#### 設置步驟

*  ```git clone https://github.com/Zero6992/chatGPT-discord-bot.git``` 將專案 clone 下來

* 執行 ```pip3 install -r requirements.txt``` 下載相關的套件

* **將資料夾中的檔案 `.env.example` 複製一個重新命名為 `.env`**

* 建議 Python 版本 `3.9` +

* 圖片生成需要 Google Chrome

### 第一步：建立 Discord 機器人

1. 前往 [Discord 開發者平台](https://discord.com/developers/applications) 建立一個 Application

2. 在該 Application 中建立一個 Discord 機器人

3. 從機器人設定中拿到 token，並存到 `.env` 中的 `DISCORD_BOT_TOKEN`

4. 打開 MESSAGE CONTENT INTENT 調整至 `ON`

5. 通過 OAuth2 URL 生成網址邀請你的機器人加入你的伺服器

### 第二步：在本地環境運行機器人

1. 打開終端或命令提示字元
2. 移動到您安裝 ChatGPT Discord 機器人的目錄
3. 執行 `python3 main.py` 或 `python main.py` 來運行機器人

### 第二步：使用 Docker 運行機器人

1. `docker compose up -d` 建立 Docker image並運行 Docker container
2. `docker logs -t chatgpt-discord-bot` 檢查機器人是否正常運行

   ### 停止機器人

   * 執行 `docker ps` 查看運行中的服務列表
   * 執行 `docker stop <BOT CONTAINER ID>` 停止運行中的機器人


### 建議： OpenAI API

1. 登入 [OpenAI API 網站](https://platform.openai.com/api-keys) 獲取 API key
2. 將 API key 貼到 `.env` 文件中的 `OPENAI_KEY` 欄位
3. 將 `.env` 文件中的 `OPENAI_ENABLED` 設為 `True`

> **注意**
> GPT-4 API 有限制存取權，詳細請參閱 [OpenAI 說明文檔](https://help.openai.com/en/articles/7102672-how-can-i-access-gpt-4)

---

### 圖片生成

#### 使用 Microsoft Bing 生成圖片

1. [Bing 聊天](https://www.bing.com/chat) 登入
2. 用 `F12` 打開開發者工具
3. 打開 `Application` > Cookies
4. 從 Cookies 中複製 `_U` 值並貼到 `.env` 文件中的 `BING_COOKIE` 後

#### 使用 Google Gemini 生成圖片

1. [Google Gemini](https://gemini.google.com/app) 登入
2. 用 `F12` 打開開發者工具
3. 打開 `Application` > Cookies
4. 從 Cookies 中複製 `__Secure-1PSID` 值並貼到 `.env` 文件中的 `GOOGLE_PSID` 後

---
#### 指令

* `/chat [訊息]` 和 ChatGPT/Gemini 聊天
* `/draw [提示]` 用 Gemini/OpenAI/Bing 生成圖像
* `/switchpersona [人格]` 切換不同的 ChatGPT 人格模式
* `/private` 切換到私人模式
* `/public` 切換到公開模式
* `/replyall` 在replyall模式和預設模式之間切換
* `/reset` 清除 ChatGPT 的對話記錄
* `/chat-model` 切換聊天模型
   * `gpt-4`: GPT-4 模型
   * `Gemini`: Google Gemini 模型

### 特殊功能

#### 切換人格
* 使用 `/switchpersona` 指令來切換不同人格模式，如 `random`、`standard`、`dan` 等

#### 模式
* `public mode (default)`: 機器人直接在頻道回覆訊息
* `private mode`: 機器人回覆的訊息只有使用指令的人能看到
* `replyall mode`: 機器人將回覆頻道中的所有訊息，不需要使用斜線指令（`/chat` 將不可用）

> 更多信息和更新： [GitHub頁面](https://github.com/Zero6992/chatGPT-discord-bot)。

