---
title: "建立自己的ChatGPT Discord機器人"
description: "ChatGPT Discord 機器人"
date: 2023-03-09T14:49:00+08:00
draft: false
tags: [ChatGPT Discord 機器人, ChatGPT, Discord 機器人, gpt-4, Gemini-Pro]
---

> 此頁面的程式碼開源在 [https://github.com/Zero6992/chatGPT-discord-bot](https://github.com/Zero6992/chatGPT-discord-bot)

---

這是一個讓你能夠在 Discord 架設你自己的 AI 機器人的教學

* 目前聊天支援 Google Gemini-Pro, OPENAI GPT-4
* 圖片生成支援 Bing, Google Gemini, OPENAI DALLE-3

#### ⚠️ ⚠️ ⚠️ 安裝前請注意 ⚠️ ⚠️ ⚠️

* 執行 ```git clone https://github.com/Zero6992/chatGPT-discord-bot.git``` 將專案 clone 下來

* 執行 ```pip3 install -r requirements.txt``` 下載相依套件

* **將資料夾中的檔案 `.env.example` 重新命名為 `.env`**

* 建議 Python 版本 `3.9` +

### 步驟一：創立一個 Discord 機器人

1. 打開 https://discord.com/developers/applications 創建一個 application

2. 在該 application 下建立一個 Discord 機器人

3. 從機器人設定中拿到 token，並存到 `.env` 中的 `DISCORD_BOT_TOKEN`

4. 打開 MESSAGE CONTENT INTENT 調整至 `ON`

5. 通過 OAuth2 URL 生成網址邀請你的機器人加入你的伺服器

### 步驟二：運行機器人

* 在一般桌機上運行機器人：
  1. 打開cmd
  2. 到你安裝此專案的資料夾底下
  3. 執行 `python3 main.py` or `python main.py`

* Docker 運行機器人：
  1. 使用 `docker compose up -d` 創建一個 Docker 映像檔 & 運行 Docker 容器
  2. 檢查機器人是否運行正常 `docker logs -t chatgpt-discord-bot`

### 圖像生成

![image](https://github.com/Zero6992/chatGPT-discord-bot/assets/89479282/e20533a4-a563-4fcd-8ec5-d5beb20ae72a)

* OpenAI DALLE3 圖像生成（需要 GPT Plus 帳號）
   1. 登入你的 GPT PLUS 帳號
   2. 打開  https://chat.openai.com/api/auth/session
   3. 將其中的`access_token`貼到`.env`中`OPENAI_TOKEN`後方

* Microsoft Bing 圖像生成
   1. 打開 https://www.bing.com/chat 登入你的 microsoft 帳號
   2. 打開 `F12` > 打開 `Application tab` 點擊 `Cookies`
   3. 找到 `_U` 將他的值貼到`.env`中`BING_COOKIE`的後方

* Google Gemini 圖像生成
   1. 打開 https://gemini.google.com/app 登入你的 google 帳號
   2. 打開 `F12` > 打開 `Application tab` 點擊 `Cookies`
   3. 找到 `__Secure-1PSID` 將他的值貼到`.env`中`GOOGLE_PSID`的後方

### 自訂選項

* 設置系統提示：修改 `system_prompt.txt` 中的內容，設置機器人啟動時的自動提示。
* 取消日誌記錄：將 `.env` 中的 `LOGGING` 值設為 False。

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

#### 注意事項
* 某些人格模式可能生成不適當或令人不安的內容，請自行承擔使用風險。

> 更多信息和更新，請參閱 [GitHub頁面](https://github.com/Zero6992/chatGPT-discord-bot)。

