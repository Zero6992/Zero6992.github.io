---
title: "建立自己的ChatGPT Discord機器人"
description: "ChatGPT Discord 機器人"
date: 2023-03-10T15:49:00+08:00
draft: false
tags: [ChatGPT Discord 機器人, ChatGPT, Discord 機器人]
---

> 此頁面程式碼在 [https://github.com/Zero6992/chatGPT-discord-bot](https://github.com/Zero6992/chatGPT-discord-bot)

---

嗨! 這是一個讓你能夠在Discord架設你自己的 AI 機器人的教學文章(目前支援ChatGPT, Bing, Bard)，廢話不多說就進入正題了

### 設置

#### ⚠️ ⚠️ ⚠️ 安裝前請注意 ⚠️ ⚠️ ⚠️

* 執行 ```git clone https://github.com/Zero6992/chatGPT-discord-bot.git``` 將專案 clone 下來

* 執行 ```pip3 install -r requirements.txt``` 下載相依套件

* **將資料夾中的檔案 `.env.dev` 重新命名為 `.env`**

* 建議的 Python 版本 `3.9` ~ `3.11`
#### 第一步：創立一個 Discord 機器人

1. 前往 https://discord.com/developers/applications 創建一個 application
2. 在該application下建立一個 Discord 機器人
3. 從機器人設置中獲取token，如下圖

   ![image](https://user-images.githubusercontent.com/89479282/205949161-4b508c6d-19a7-49b6-b8ed-7525ddbef430.png)
4. 然後將token存儲到 `.env` 中的 `DISCORD_BOT_TOKEN`

   <img height="190" width="390" alt="image" src="https://user-images.githubusercontent.com/89479282/222661803-a7537ca7-88ae-4e66-9bec-384f3e83e6bd.png">

5. 打開 MESSAGE CONTENT INTENT 並調至 `ON`

   ![image](https://user-images.githubusercontent.com/89479282/205949323-4354bd7d-9bb9-4f4b-a87e-deb9933a89b5.png)

6. 通過 OAuth2 URL 生成器邀請你的機器人加入你的伺服器

   ![image](https://user-images.githubusercontent.com/89479282/205949600-0c7ddb40-7e8247a0-b59a-b089f929d177.png)

到這裡你就完成建立了你的Discord機器人，接下來我們要將Discord機器人連上AI，第二步有許多選項，你只需要完成一種就能使用其中一種model
> 目前總共有支援以下模型
>   * `OFFICIAL-GPT-3.5`: GPT-3.5 模型
>   * `OFFICIAL-GPT-4.0`: GPT-4.0 模型（你的OpenAI帳號要可以調用 gpt-4 模型）
>   * `Website ChatGPT-3.5`: 網站 ChatGPT-3.5 模型（非官方，反向ChatGPT網站的API，必須要是ChatGPT plus 帳號）
>   * `Website ChatGPT-4.0`: 網站 ChatGPT-4.0 模型（非官方，反向ChatGPT網站的API，必須要是ChatGPT plus 帳號）
>   * `Bard`: Google Bard 模型（免費）
>   * `Bing`: Mircrosoft Bing 模型（免費）

### 步驟二：官方 API 驗證

#### 生成一個 OpenAI API 密鑰
1. 進入 https://beta.openai.com/account/api-keys

2. 點擊 Create new secret key

   ![image](https://user-images.githubusercontent.com/89479282/207970699-2e0cb671-8636-4e27-b1f3-b75d6db9b57e.PNG)

3. 將 SECRET KEY 存儲到 `.env` 中的 `OPENAI_API_KEY`

4. 現在你就完成 OpenAI API 的密鑰驗證，可以使用 OFFICIAL-GPT-3.5 以及 OFFICIAL-GPT-4.0 了

5. 如果你只需要使用 GPT 模型，可以直接前往步驟三
### 步驟二：Website ChatGPT 認證 - 2 種方法

> ⚠️ 注意 ⚠️
> Website ChatGPT 目前只有支援 ChatGPT Plus 帳號

#### 方法 1: 電子郵件/密碼認證（不支援 Google/Microsoft 帳戶）

1. 在 https://chat.openai.com/chat 上創建一個帳戶並登入

2. 使用 `F12` 打開控制台
3. 打開 `Application` > Cookies

   ![image](https://user-images.githubusercontent.com/89479282/229298001-41ab4f61-5b79-4c65-b08c-708ee6fe2304.png)

4. 從 cookies 中複製 `_puid` 並粘貼到 `.env` 下的 `PUID`

5. 將你的電子郵件保存到 `.env` 中的 `OPENAI_EMAIL`

6. 將你的密碼保存到 `.env` 中的 `OPENAI_PASSWORD`

7. 完成Website ChatGPT驗證，你可以調用 Website ChatGPT-3.5 與 Website ChatGPT-4.0 了

8. 如果你只需要使用 GPT 模型，可以直接前往步驟三

### 方法 2: ACCESS token 認證
1. 打開 https://chat.openai.com/api/auth/session

2. 使用 `F12` 打開控制台

3. 打開 `Application` > Cookies

   ![image](https://user-images.githubusercontent.com/89479282/229298001-41ab4f61-5b79-4c65-b08c-708ee6fe2304.png)

4. 從 cookies 中複製 `_puid` 粘貼到 `.env` 下的 `PUID`

5. 從 cookies 中複製 `accessToken` 粘貼到 `.env` 下的 `ACCESS_TOKEN`

7. 完成Website ChatGPT驗證，你可以調用 Website ChatGPT-3.5 與 Website ChatGPT-4.0 了

8. 如果你只需要使用 GPT 模型，可以直接前往步驟三
### 步驟二：Google Bard 認證
1. 前往 https://bard.google.com/

2. 使用 `F12` 打開控制台

3. 打開 `Application` > Cookies

4. 從 cookies 中複製 `__Secure-1PSID` 粘貼到 `.env` 下的 `BARD_SESSION_ID`

5. 完成Bard驗證，你可以調用 Bard 模型了

6. 如果你只需要使用 Bard 模型，可以直接前往步驟三
### 步驟二：Microsoft Bing 驗證
1. 將檔案 `cookies.dev.json` 重新命名為 `cookies.json`

2. 前往 https://bing.com/chat 並使用 Microsoft 帳戶登入

3. 使用 [Cookie Editor](https://chrome.google.com/webstore/detail/cookie-editor/hlkenndednhfkekhgcdicdfddnkalmdm) 或相似的擴充套件export cookies

4. 在 cookies.json 中全選貼上

5. 完成Bing驗證，你可以調用 Bing 模型了

### 步驟三：在桌面上運行機器人

1. 打開終端機或命令提示符

2. 到你安裝此專案的目錄底下

3. 運行 `python3 main.py` 或是 `python main.py` 以啟動機器人

你成功在Discord架設你的AI機器人了 🚀🚀

### 步驟三：使用 Docker 運行機器人

1. 使用 `docker compose up -d` 構建 Docker 映像並運行 Docker 容器

2. 檢查機器人是否運行正常 `docker logs -t chatgpt-discord-bot`

   #### 停止機器人：

   * 運行 `docker ps` 以查看運行中的服務列表
   * 運行 `docker stop <BOT CONTAINER ID>` 以停止運行的機器人

> 可以利用像heroku這類雲端供應商來host機器人

#### 自訂義設置：取消日誌記錄(logging)

* 將 `.env` 中的 `LOGGING` 值設為 False
#### 自訂義設置：設置系統提示(system prompt)

* 系統提示將在機器人首次啟動或重置時調用
* 你可以通過修改 `system_prompt.txt` 中的內容來設置它 
   * 例如：你可以寫入："請之後的回答都使用繁體中文" 以減少重複需要需要輸入的prompt
* 文件中的所有文字都將作為預設提示發送給機器人
* 在你的 discord 頻道中獲取 ChatGPT 的第一條消息！（不支援Official model）

   1. 右鍵單擊要接收消息的頻道，選擇 `Copy ID`

        ![channel-id](https://user-images.githubusercontent.com/89479282/207697217-e03357b3-3b3d-44d0-b880-163217ed4a49.PNG)

   2. 粘貼到 `.env` 下的 `DISCORD_CHANNEL_ID`


#### 現有功能

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
* `/help` 幫助