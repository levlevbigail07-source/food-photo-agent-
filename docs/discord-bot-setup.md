# Discord Bot 設定步驟

這份文件帶你做這幾件事:

1. 在 Discord Developer Portal 建立 application 和 bot
2. 開啟 Message Content Intent
3. 取得 bot token
4. 設定權限,並把 bot 邀請進你自己的 Server

請先完成 `docs/discord-server-setup.md`,你需要有一個自己的 Server。

---

## ⚠️ 最重要的提醒:bot token 是密碼

**bot token 等於你的 bot 的帳號密碼。** 拿到 token 的人可以假冒你的 bot 做任何事。

| ❌ 絕對不要 | 原因 |
|---|---|
| 分享給任何人(包含同學、朋友) | 他們可以完全控制你的 bot |
| 貼進 Claude Code 的對話 | 對話內容會被傳送出去並留下紀錄 |
| 貼進任何聊天室、截圖、影片、簡報 | 一旦外流就收不回來 |
| commit 或 push 到 GitHub | GitHub 上的內容(包含歷史紀錄)任何人都可能看到,而且有程式專門在掃描外洩的 token |
| 寫死在程式碼裡 | 很容易不小心一起 commit |

**正確做法:** 把 token 只放在你電腦上的 `.env` 檔案(做法見第六段)。這個專案的 `.gitignore` 已經設定不會把 `.env` 上傳。

**如果 token 不小心外流了:** 立刻回到 Developer Portal,在 Bot 頁面按 **Reset Token**(見第四段),舊的 token 就會失效。如果已經 push 到 GitHub,重設 token 之後還是要這樣做,因為舊紀錄刪不乾淨。

---

## 一、建立 Application 和 Bot

Discord 把「應用程式(Application)」和「機器人(Bot)」分開。你要先建立 application,bot 是它裡面的一個功能。

1. 用瀏覽器打開 **https://discord.com/developers/applications**。
2. 用你的 Discord 帳號登入(如果要求授權,按「授權」)。
3. 點右上角藍紫色的 **「New Application」(新增應用程式)**。
4. 跳出視窗:
   - **Name**:輸入 bot 的名字,例如 `food-photo-agent`。這個名字之後會顯示在 Discord 裡。
   - 勾選同意 **Developer Terms of Service**(開發者服務條款)和 **Developer Policy**(開發者政策)的方框。
5. 點 **「Create」(建立)**。
6. 如果跳出「我不是機器人」的驗證,依指示完成。

建立後你會進到這個 application 的設定頁面,左邊有一排選單(General Information、Installation、OAuth2、Bot…)。

### 確認 Bot 已存在

1. 點左邊選單的 **「Bot」**。
2. 新的 application 通常會自動附帶一個 bot,畫面上會看到 bot 的名稱、頭像和 **Token** 區塊。
   - 如果畫面上有「Add Bot」(新增 Bot)按鈕,點它,再按「Yes, do it!」確認。

### 建議把 Public Bot 關掉

在 Bot 頁面往下找到 **「Public Bot」** 的開關,把它**關閉**(變成灰色)。
- 意思:只有你自己能把這個 bot 邀請進伺服器,別人不能隨便把它加到別的伺服器。
- 如果頁面最下面出現「Save Changes」(儲存變更)的提示條,記得按。

---

## 二、開啟 Message Content Intent(並了解為什麼)

### 這是什麼?為什麼需要?

Discord 為了保護隱私,預設**不讓 bot 讀取伺服器頻道裡的訊息文字內容**。bot 只知道「有人傳了一則訊息」,但看不到內容。

這個 agent 需要讀你的文字,例如:
- 「這次用 TikTok、英文」(臨時指定平台和語言)
- 「以後預設用 YouTube Shorts」(修改預設偏好)
- 「這是我自己做的拿鐵」(補充說明)

沒有這個權限,bot 在伺服器頻道裡讀到的文字會是空白,它就沒辦法理解你想要什麼。所以必須打開 **Message Content Intent**。

> 補充:在 DM(私人訊息)裡,bot 本來就可以讀到對話內容,不需要這個開關。但你們要在伺服器的頻道使用 bot,所以需要打開。打開它沒有壞處。

### 怎麼開

1. 在 Developer Portal,左邊選單點 **「Bot」**。
2. 往下捲,找到 **「Privileged Gateway Intents」** 區塊。
3. 你會看到三個開關:
   - Presence Intent(**不要開**)
   - Server Members Intent(**不要開**)
   - **Message Content Intent**(**打開它**:點右邊的開關,變成藍色或綠色)
4. 畫面下方會出現「Save Changes」(儲存變更)的提示條,點 **「Save Changes」**。

> 只開 Message Content Intent,另外兩個不需要,不要開。開越少越安全。
>
> 另外,Discord 規定 bot 如果加入超過 100 個伺服器才需要另外申請審核。你的 bot 只在自己的伺服器,不會遇到這個限制。

---

## 三、取得 bot token

1. 在 Developer Portal,左邊選單點 **「Bot」**。
2. 找到 **「Token」** 區塊,點 **「Reset Token」(重設 Token)**。
   - Discord 為了安全,**只有在你剛產生的那一刻才會顯示完整的 token**,之後再也看不到,只能重設。
3. 跳出確認視窗,點 **「Yes, do it!」**。如果要求輸入密碼或兩步驟驗證碼,請輸入。
4. 畫面上會出現一長串文字,這就是 **bot token**。點旁邊的 **「Copy」(複製)**。
5. **馬上**貼到一個安全的地方暫存,例如:
   - 你電腦上的記事本,存成不會上傳的檔案(**不要存在專案資料夾裡**),或
   - 密碼管理程式

   等一下第六段會把它放進 `.env`。
6. 貼完之後**不要截圖、不要貼到任何對話裡**(包含 Claude Code)。

> 如果你按了 Copy 卻忘記貼,或複製到別的東西了,沒關係:再按一次 Reset Token,重新產生一個新的就好。

---

## 四、需要的權限

bot 要在頻道裡「看得到訊息、收到照片、回覆文字」,不需要任何管理權限。

| 權限(英文) | 中文意思 | 為什麼需要 |
|---|---|---|
| **View Channels** | 檢視頻道 | 看得到頻道,才能收到訊息 |
| **Send Messages** | 傳送訊息 | 回覆處理結果 |
| **Read Message History** | 讀取訊息記錄 | 補處理 bot 離線期間收到的訊息和照片 |
| **Attach Files** | 附加檔案 | 之後如果要回傳圖片時會用到(第一版回覆只有文字,但先開著,以後不用重新邀請) |

**不要給 Administrator(管理員)權限。** 給太多權限,萬一 token 外流,別人能做的壞事就更多。

---

## 五、把 bot 邀請進你自己的 Server

1. 在 Developer Portal,左邊選單點 **「OAuth2」**。
2. 往下找到 **「OAuth2 URL Generator」** 區塊(有些版本是在 OAuth2 底下的子選單 **URL Generator**)。
3. 在 **SCOPES(範圍)** 的清單裡,勾選 **`bot`**。
   - **不要勾別的**,例如 `applications.commands` 這次用不到。
4. 勾選 `bot` 之後,下面會多出 **「BOT PERMISSIONS」(Bot 權限)** 區塊。勾選第四段表格裡的四個權限:
   - View Channels
   - Send Messages
   - Read Message History
   - Attach Files
5. 捲到最下面,**「GENERATED URL」(產生的網址)** 欄位會出現一條很長的網址。點旁邊的 **「Copy」**。
6. 打開瀏覽器的新分頁,貼上這條網址,按 Enter。
7. 進到 Discord 授權頁面:
   - **「新增至伺服器」(Add to server)** 下拉選單:選你自己建立的那個伺服器。
   - 點 **「繼續」(Continue)**。
8. 下一頁會列出 bot 將取得的權限,確認只有你勾的那幾項,點 **「授權」(Authorize)**。
9. 完成「我不是機器人」驗證(如果有)。

### 怎麼確認成功

回到 Discord,看你的伺服器右邊的**成員列表**,應該會看到你的 bot 名稱,旁邊有一個小小的 **「APP」(應用程式)** 標籤,狀態是**離線**(灰色圓點)。

離線是正常的,因為程式還沒有執行。等之後 bot 程式啟動後,它才會變成上線(綠色圓點)。

### 如果 bot 看不到那個頻道

如果你之後把頻道設成私人頻道,要手動讓 bot 進去:右鍵頻道 → **「編輯頻道」(Edit Channel)** → **「權限」(Permissions)** → 新增 bot,並允許上面四個權限。

---

## 六、把 token 放進 `.env`(之後設定程式時才做)

現在**先不用做**這一段,等我們開始寫程式、需要設定時再做。先在這裡記下做法,避免到時候不小心外流:

1. 在專案資料夾(`food-photo-agent-`)最外層,建立一個檔名剛好是 **`.env`** 的檔案(前面有一個點,沒有其他副檔名)。
   - Windows 小提醒:用記事本另存新檔時,「存檔類型」要選「所有檔案」,否則會變成 `.env.txt`。
2. 檔案內容大致長這樣(之後會依程式需要調整):

   ```
   DISCORD_TOKEN=貼上你的token
   CHANNEL_ID=貼上你的頻道ID
   ALLOWED_USER_IDS=貼上你的使用者ID
   ```

3. 這個專案的 `.gitignore` 已經包含 `.env`,Git 不會追蹤它。
4. 每次要 commit 之前,可以檢查一下:在終端機輸入 `git status`,**如果清單裡出現 `.env`,絕對不要 commit**,先停下來檢查 `.gitignore`。

---

## 七、檢查清單

做完後,對照一下:

- [ ] Developer Portal 有一個 application,Bot 頁面可以看到 bot
- [ ] Message Content Intent 已開,而且有按 Save Changes
- [ ] Presence Intent 和 Server Members Intent 沒有開
- [ ] 已複製 bot token,放在安全的地方,**沒有貼給任何人、沒有貼進 Claude Code**
- [ ] bot 權限只有 View Channels、Send Messages、Read Message History、Attach Files
- [ ] bot 已經出現在你的伺服器成員列表(離線)
- [ ] 專案資料夾裡沒有 token,`git status` 沒有出現 `.env`

都打勾了,就可以回報,我們再進行下一步。
