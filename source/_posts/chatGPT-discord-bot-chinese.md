---
title: "建立自己的ChatGPT Discord機器人"
description: "ChatGPT Discord 機器人"
date: 2023-03-09T14:49:00+08:00
draft: false
tags: [ChatGPT Discord 機器人, ChatGPT, Discord 機器人, gpt-4, Gemini-Pro]
---

把 ChatGPT、Gemini 接到自己的 Discord 機器人，從零架到能跑的最短路徑。

* 聊天可選 Google Gemini-Pro 或 OpenAI GPT-4
* 圖片生成可選 Bing 或 Google Gemini


> 原始碼放在 [https://github.com/Zero6992/chatGPT-discord-bot](https://github.com/Zero6992/chatGPT-discord-bot)



#### 安裝前

*  ```git clone https://github.com/Zero6992/chatGPT-discord-bot.git``` 把專案抓下來

* 進到資料夾後執行 ```pip3 install -r requirements.txt``` 安裝相依套件

* **把 `.env.example` 複製一份，改名成 `.env`**

* Python 建議 `3.9` 以上

* 圖片生成功能需要 Google Chrome

### 第一步：建立 Discord 機器人

1. 到 [Discord 開發者平台](https://discord.com/developers/applications) 開一個 Application

2. 在這個 Application 底下新增 Discord 機器人

3. 複製機器人設定頁的 token，貼到 `.env` 的 `DISCORD_BOT_TOKEN`

4. 把 MESSAGE CONTENT INTENT 切到 `ON`

5. 用 OAuth2 URL 產生器產生邀請連結，把機器人拉進你的伺服器

### 第二步：在本機跑機器人

1. 開啟終端機或命令提示字元
2. 切到 ChatGPT Discord 機器人的資料夾
3. 執行 `python3 main.py` 或 `python main.py`

### 第二步：用 Docker 跑機器人

1. `docker compose up -d` 建 Docker image 並啟動 container
2. `docker logs -t chatgpt-discord-bot` 看一下機器人有沒有正常運作

   ### 停掉機器人

   * `docker ps` 列出執行中的服務
   * `docker stop <BOT CONTAINER ID>` 關掉對應的 container


### 建議：開 OpenAI API

1. 到 [OpenAI API 網站](https://platform.openai.com/api-keys) 取得 API key
2. 把 API key 貼到 `.env` 的 `OPENAI_KEY` 欄位
3. 把 `.env` 的 `OPENAI_ENABLED` 改成 `True`

> **注意**
> GPT-4 API 有存取門檻，細節看 [OpenAI 官方說明](https://help.openai.com/en/articles/7102672-how-can-i-access-gpt-4)

---

### 圖片生成

#### 用 Microsoft Bing

1. 登入 [Bing 聊天](https://www.bing.com/chat)
2. 按 `F12` 打開開發者工具
3. 切到 `Application` > Cookies
4. 把 Cookies 裡的 `_U` 值貼到 `.env` 的 `BING_COOKIE`

#### 用 Google Gemini

1. 登入 [Google Gemini](https://gemini.google.com/app)
2. 按 `F12` 打開開發者工具
3. 切到 `Application` > Cookies
4. 把 Cookies 裡的 `__Secure-1PSID` 值貼到 `.env` 的 `GOOGLE_PSID`

---
#### 指令

* `/chat [訊息]`：跟 ChatGPT / Gemini 對話
* `/draw [提示]`：用 Gemini / OpenAI / Bing 產生圖片
* `/switchpersona [人格]`：切換 ChatGPT 人格模式
* `/private`：切到私人模式
* `/public`：切到公開模式
* `/replyall`：在 replyall 與預設模式之間切換
* `/reset`：清空 ChatGPT 的對話紀錄
* `/chat-model`：切換聊天模型
   * `gpt-4`：GPT-4
   * `Gemini`：Google Gemini

### 其他功能

#### 切換人格
用 `/switchpersona` 切換 `random`、`standard`、`dan` 等不同人格。

#### 模式說明
* `public mode`（預設）：機器人直接在頻道回覆
* `private mode`：機器人的回覆只有下指令的人看得到
* `replyall mode`：機器人會回覆頻道裡所有訊息，這時候 `/chat` 不能用

> 更新與更多細節請看 [GitHub 頁面](https://github.com/Zero6992/chatGPT-discord-bot)。

