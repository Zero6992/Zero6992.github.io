---
title: "建立自己的ChatGPT Discord機器人"
description: "ChatGPT Discord 機器人"
date: 2023-03-10T15:49:00+08:00
draft: false
tags: [ChatGPT Discord 機器人, ChatGPT, Discord 機器人]
---

> 此頁面程式碼在 [https://github.com/Zero6992/chatGPT-discord-bot](https://github.com/Zero6992/chatGPT-discord-bot)

---

> 2023-04-01 只有 Plus 帳戶可以使用非官方模型
>
> 2023-03-27 現在支援 Bard 模型

## 功能

* `/chat [訊息]` 和 ChatGPT 聊天！
* `/draw [提示]` 用 Dalle2 模型生成圖像 (必須填入Openai API key)
* `/switchpersona [角色]` 切換可選的 ChatGPT 人格模式
   * `random`: 隨機選擇一個角色
   * `chatGPT`: 標準的 chatGPT 模式
   * `dan`: Dan 模式 11.0，惡名昭彰的 Do Anything Now 模式
   * `sda`: 更自由的 Superior DAN 模式
   * `confidant`: 邪惡的信任顧問
   * `based`: BasedGPT v2，性感的 gpt
   * `oppo`: OPPO 會說與 chatGPT 完全相反的話
   * `dev`: 開發者模式，v2 開發者模式已啟用

* `/private` ChatGPT 切換到私人模式
* `/public` ChatGPT 切換到公開模式
* `/replyall` ChatGPT 在replyall模式和預設模式之間切換
* `/reset` 清除 ChatGPT 的所有記憶
* `/chat-model` 切換不同的聊天模型
   * `OFFICIAL-GPT-3.5`: GPT-3.5 模型
   * `OFFICIAL-GPT-4.0`: GPT-4.0 模型（確保你的帳戶可以訪問 gpt-4 模型）
   * `Website ChatGPT-3.5`: 網站 ChatGPT-3.5 模型（非官方）
   * `Website ChatGPT-4.0`: 網站 ChatGPT-4.0 模型（非官方）（如果你有 Plus 帳戶的話可以用）
   * `Bard`: Google Bard 模型

### 聊天

![image](https://user-images.githubusercontent.com/89479282/206497774-47d960cd-1aeb-4fba-9af5-1f9d6ff41f00.gif)

### 繪圖

![image](https://user-images.githubusercontent.com/91911303/223772051-13f840d5-99ef-4762-98d2-d15ce23cbbd5.png)

### 切換角色

> **警告**
>
> 使用某些角色可能會產生粗俗或令人不安的內容。請自行承擔風險。

![image](https://user-images.githubusercontent.com/91911303/223772334-7aece61f-ead7-4119-bcd4-7274979c4702.png)

### 模式

* `public mode (default)` 機器人直接在頻道上回覆

  ![image](https://user-images.githubusercontent.com/89479282/206565977-d7c5d405-fdb4-4202-bbdd-715b7c8e8415.gif)

* `private mode` 只有使用命令的人才能看到機器人的回覆

  ![image](https://user-images.githubusercontent.com/89479282/206565873-b181e600-e793-4a94-a978-47f806b986da.gif)

* `replyall mode` 機器人將在不使用斜杠命令的情況下回覆頻道中的所有訊息（`/chat` 也將不可用）

   > **警告**
   > 在 `replyall` 模式下，機器人很容易被觸發，可能導致程序失敗

# 設置

## 安裝前注意事項

* 執行 ```pip3 install -r requirements.txt```

* **將文件 `.env.dev` 重命名為 `.env`**

* 建議的 Python 版本 `3.10`
## 步驟1：創立一個 Discord 機器人

1. 前往 https://discord.com/developers/applications 創建一個應用程式
2. 在該應用程式下建立一個 Discord 機器人
3. 從機器人設置中獲取token

   ![image](https://user-images.githubusercontent.com/89479282/205949161-4b508c6d-19a7-49b6-b8ed-7525ddbef430.png)
4. 將token存儲到 `.env` 中的 `DISCORD_BOT_TOKEN`

   <img height="190" width="390" alt="image" src="https://user-images.githubusercontent.com/89479282/222661803-a7537ca7-88ae-4e66-9bec-384f3e83e6bd.png">

5. 打開 MESSAGE CONTENT INTENT 並調至 `ON`

   ![image](https://user-images.githubusercontent.com/89479282/205949323-4354bd7d-9bb9-4f4b-a87e-deb9933a89b5.png)

6. 通過 OAuth2 URL 生成器邀請你的機器人加入你的伺服器

   ![image](https://user-images.githubusercontent.com/89479282/205949600-0c7ddb40-7e8247a0-b59a-b089f929d177.png)
## 步驟2：官方 API 認證

### 生成一個 OpenAI API 密鑰
1. 前往 https://beta.openai.com/account/api-keys

2. 點擊 Create new secret key

   ![image](https://user-images.githubusercontent.com/89479282/207970699-2e0cb671-8636-4e27-b1f3-b75d6db9b57e.PNG)

3. 將 SECRET KEY 存儲到 `.env` 中的 `OPENAI_API_KEY`

4. 現在你已經準備好 [步驟3](#step-3-run-the-bot-on-the-desktop) 了

## 步驟2：Website ChatGPT 認證 - 2 種方法

* 2023-04-01：現在只支持 Plus 帳戶

### 電子郵件/密碼認證（不支持 Google/Microsoft 帳戶）

1. 在 https://chat.openai.com/chat 上創建一個帳戶並打開它

2. 使用 `F12` 打開控制台
3. 打開 `Application` 標籤 > Cookies

   ![image](https://user-images.githubusercontent.com/89479282/229298001-41ab4f61-5b79-4c65-b08c-708ee6fe2304.png)

4. 從 cookies 中複製 `_puid` 的值並將其粘貼到 `.env` 下的 `PUID`

5. 將你的電子郵件保存到 `.env` 中的 `OPENAI_EMAIL`

6. 將你的密碼保存到 `.env` 中的 `OPENAI_PASSWORD`

7. 現在你已經準備好 [步驟3](#step-3-run-the-bot-on-the-desktop) 了

### ACCESS token 認證
1. 打開 https://chat.openai.com/api/auth/session

2. 使用 `F12` 打開控制台

3. 打開 `Application` 標籤 > Cookies

   ![image](https://user-images.githubusercontent.com/89479282/229298001-41ab4f61-5b79-4c65-b08c-708ee6fe2304.png)

4. 從 cookies 中複製 `_puid` 的值並將其粘貼到 `.env` 下的 `PUID`

5. 從 cookies 中複製 `accessToken` 的值並將其粘貼到 `.env` 下的 `ACCESS_TOKEN`

6. 現在你已經準備好 [步驟3](#step-3-run-the-bot-on-the-desktop) 了

## 步驟2：Google Bard 認證
1. 前往 https://bard.google.com/

2. 使用 `F12` 打開控制台

3. 打開 `Application` 標籤 > Cookies

4. 從 cookies 中複製 `__Secure-1PSID` 的值並將其粘貼到 `.env` 下的 `BARD_SESSION_ID`

5. 現在你已經準備好 [步驟3](#step-3-run-the-bot-on-the-desktop) 了

## 步驟3：在桌面上運行機器人

1. 打開終端或命令提示符

2. 到你安裝 ChatGPT Discord 機器人的目錄

3. 運行 `python3 main.py` 以啟動機器人

## 步驟3：使用 Docker 運行機器人

1. 使用 `docker compose up -d` 構建 Docker 映像並運行 Docker 容器

2. 檢查機器人是否運行正常 `docker logs -t chatgpt-discord-bot`

   ### 停止機器人：

   * 運行 `docker ps` 以查看運行中的服務列表
   * 運行 `docker stop <BOT CONTAINER ID>` 以停止運行的機器人

> 可以使用heroku來host機器人

### 聊天愉快！
## 可選：禁用日誌記錄(logging)

* 將 `.env` 中的 `LOGGING` 值設置為 False
## 可選：設置系統提示(system prompt)

* 系統提示將在機器人首次啟動或重置時調用
* 你可以通過修改 `system_prompt.txt` 中的內容來設置它
* 文件中的所有文字都將作為預設提示發送給機器人
* 在你的 discord 頻道中獲取 ChatGPT 的第一條消息！（不支援官方模型）

   1. 右鍵單擊要接收消息的頻道，選擇 `Copy ID`

        ![channel-id](https://user-images.githubusercontent.com/89479282/207697217-e03357b3-3b3d-44d0-b880-163217ed4a49.PNG)

   2. 將其粘貼到 `.env` 下的 `DISCORD_CHANNEL_ID`




