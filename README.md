# Build with AI｜AI Studio 作品集實作與 Cloud Run 部署

GDG on Campus NCU｜2026/10/08

**目標：領取 Credits → 建立作品集 → Firestore 保存資料 → 部署到 Cloud Run**

> 本版使用活動 Credits 所屬的 GCP 專案進行標準部署（standard deployment）  
> Credits 的金額、期限與可抵扣服務，以本次官方通知及帳單頁為準  
> AI Studio 建置、Gemini API 呼叫與 Cloud Run 部署各自有用量與計費規則；本課作品集本身不串接 Gemini API

[TOC]

## 1. 領取 Google Cloud Credits

### 開啟兌換頁

使用無痕分頁開啟**講師提供的兌換連結**，登入個人 Google 帳號，點選 **CLICK HERE TO ACCESS YOUR CREDITS**

### 填寫資料並確認領取

填寫姓名、確認 Gmail，點選 **ACCEPT AND CONTINUE**，確認出現 **Credit successfully applied**

![填寫資料與 Credits 領取成功畫面](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/02_credits_confirm.png)

## 2. 建立專案並綁定 Credits

### 開啟專案選單

進入 [Google Cloud Console](https://console.cloud.google.com/)，點選左上角 **選取專案**

![Google Cloud 選取專案入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/03_project_menu.png)

### 新增專案

點選右上角 **新增專案**

![新增專案入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/04_project_new.png)

### 輸入名稱

輸入英文專案名稱，例如 `Agentic-AI`，點選 **建立**

![輸入專案名稱並建立](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/05_project_name.png)

### 切換到新專案

等待建立完成，在通知中點選 **選取專案**

![專案建立完成通知](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/06_project_select.png)

### 開啟帳單

展開左上角選單，點選 **帳單**

![Google Cloud 帳單入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/07_billing_menu.png)

### 連結帳單帳戶

點選 **連結帳單帳戶**

![連結帳單帳戶入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/08_billing_link.png)

### 選擇活動帳戶

選擇本次活動 Credits 所屬的帳單帳戶，簡報範例名稱為 **Google Cloud Platform Trial Billing Account**

若尚未出現，重新整理並等待約 30 秒，再確認登入帳號

![選擇帳單帳戶](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/09_billing_choose.png)

### 完成綁定

確認帳戶後，點選 **Set account**

![設定專案帳單帳戶](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/10_billing_set.png)

### 確認 Credits

在帳單頁左側點選 **Credits**，確認活動額度、剩餘額度與適用範圍

![查看帳單帳戶 Credits](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/11_credits_check.png)

## 3. 進入 AI Studio Build Mode

### 建立應用程式

開啟 [Google AI Studio](https://aistudio.google.com/)，確認使用同一個 Google 帳號，選擇左側 **BUILD → New app**

右上角齒輪可開啟設定

![AI Studio Build Mode 與設定入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/12_build_home.png)

### 設定模型與偏好

- **Select model to use in Chat**：選擇課堂使用的模型
- **Framework**：選擇前端框架；依講師指示或保留預設
- **Custom instructions**：設定語氣、介面語言與開發偏好
- **Usage**：查看目前用量，課堂先使用帳號可用的免費建置額度

![AI Studio 模型、框架、指令與用量設定](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/13_build_settings.png)

> 模型名稱與選項以你的畫面及講師指示為準  
> 若建置時要求切換 **pay per request** 或儲值，先取消並請講師確認；本課將活動專案用於後面的 Cloud Run 部署

## 4. Prompt 1：建立作品集畫面

回到 **New app**，貼上以下提示詞並送出

將 `典雅 <請自行修改>` 改成你想要的介面風格

```text
製作一個可以讓我持續更新作品內容的 RWD 網站，包含個人資料與作品。
個人資料要可以更新顯示
作品集要能新增、修改、刪除
作品的內容包含：標題、文字描述、照片、影片連結(youtube 連結)......
或是其他可以幫我想想可以再放什麼
介面語言：請使用臺灣慣用詞及術語
介面風格：典雅 <請自行修改>
目標：只要先做畫面
先不要：串接 Gemini API
先不要：串接 Firebase
```

RWD（Responsive Web Design）：讓網頁版面適應不同螢幕尺寸

生成完成後，在預覽畫面測試個人資料更新、作品新增、修改與刪除

## 5. Prompt 2：串接 Firestore

依講師指示，回到**同一個應用程式的對話**，送出第二段提示詞

```text
幫我使用 Firebase Firestore 來建立個人檔案、與每個作品集的內容
```

依 AI Studio 的回覆完成需要的設定，再測試：

- 新增一件作品，重新整理後確認資料仍保留
- 修改作品，重新整理後確認更新已保存
- 刪除測試作品，重新整理後確認刪除已保存

## 6. 部署到 Cloud Run

### Cloud Run 負責什麼？

Cloud Run 是 Google 的全代管應用程式平台（fully managed application platform），在雲端執行應用程式並接收網站請求

| 元件 | 本次用途 |
| --- | --- |
| AI Studio Build | 產生程式、修改功能與預覽 |
| Firestore | 保存個人資料與作品內容 |
| Cloud Run | 執行發布後的網站，提供對外網址 |
| 活動 GCP 專案與帳單帳戶 | 歸屬部署資源，依 Credits 條件抵扣適用費用 |

Cloud Run 以容器（container）運行程式，提供自動擴縮（autoscaling）與 HTTPS 網址  
透過 AI Studio 發布時，平台會處理部署流程，課堂不需要自行撰寫 Dockerfile  
容器本機檔案可能隨執行個體停止而消失，因此作品資料繼續保存在 Firestore

參考：[Cloud Run 官方介紹](https://docs.cloud.google.com/run/docs/overview/what-is-cloud-run)

### 發布前確認

- Preview 中的作品新增、修改、刪除已正常運作
- Firestore 的資料重新整理後仍保留
- 第 2 節的 GCP 專案已連結活動帳單帳戶，記下其 **Project ID**
- 即將公開的作品只包含適合對外展示的資料

若網站目前讓所有訪客都能編輯，先在同一個 Build 對話送出：

```text
請保留目前的作品集與 Firestore 資料，補上公開發布需要的編輯權限
訪客可以閱讀，只有作品集擁有者登入後可以新增、修改及刪除

使用 Firebase Authentication 的 Google 登入
請引導我指定自己的 UID 作為擁有者，不要把第一位訪客自動設為擁有者
請在 Firestore Security Rules 或伺服器端實際驗證權限，不能只隱藏按鈕
```

設定完成後，確認擁有者可編輯、登出及其他帳號只能閱讀

### 開啟發布面板

在 AI Studio 應用程式右上角點 **Publish**

![AI Studio 右上角的 Publish 按鈕](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/20_cloud_run_publish.png)

> 以下操作圖片取自 Google Codelabs 官方範例，作品名稱與本課不同，按鈕以實際介面為準

### 選擇已綁定 Credits 的 GCP 專案

1. 在 **Deploy app on Google Cloud** 發布視窗，開啟 **Select a Cloud Project**
2. 選擇第 2 節建立的活動專案，核對 **Project ID**
3. 找不到專案時，點 **Import project**，匯入後再選取
4. 確認該專案已啟用帳單，且連結的是活動 Credits 所屬帳戶

> **專案名稱與帳單帳戶名稱是兩個不同欄位**  
> 部署時選 GCP 專案；費用會歸到該專案連結的帳單帳戶  
> 若畫面預選 **Default Gemini Project**，先核對 Project ID，再決定是否使用
>
> 本版採用已有帳單的標準部署，接續前面的活動專案  
> 若畫面只提供 Starter Tier 或要求新增付款方式，先核對帳號與專案，再請講師協助

標準部署需要 AI Studio 可存取的 GCP 專案及已啟用的帳單  
參考：[官方部署說明](https://ai.google.dev/gemini-api/docs/aistudio-deploying)

### 發布應用程式

1. 確認選擇正確專案
2. 依畫面完成必要授權與服務啟用
3. 點 **Publish App／Publish your app**
4. 等待部署完成；AI Studio 會建立對應的 Cloud Run 服務（service）
5. 看到 **Status = Ready** 後，點 **Visit／App URL** 開啟網站

![Cloud Run 部署完成，顯示 Ready 與 App URL](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/21_cloud_run_published.png)

> 圖中的 Gemini API 欄位屬於官方範例，本課作品集不需要額外接入 Gemini API  
> 若你自行加入模型功能，須另行確認該 API 專案、模型配額及費用

### 驗證正式網址

- 用無痕視窗或手機開啟 App URL，確認作品集正常顯示
- 關閉 AI Studio 編輯分頁，確認發布網址仍可存取
- 以擁有者登入新增一件作品，另一個視窗重新整理後應讀到相同內容
- 登出及其他帳號只能閱讀，無法修改作品

**Cloud Run 部署完成與 Firestore 連線成功，是兩項需要分別確認的結果**  
若 Firestore 位於另一個專案，沿用原本的資料連線與權限；將網站部署到活動專案，不會自動搬移資料庫或改變其計費歸屬

### 修改後重新發布

1. 回到 Build 修改網站
2. 在 Preview 確認修改正確
3. 開啟 **Publish** 面板，點 **Republish** 或目前介面的更新按鈕
4. 重新整理 App URL，確認新版內容已上線

修改程式與版面後需要重新發布；透過網站更新 Firestore 內的作品內容，通常直接儲存即可

操作參考：[Google Codelabs：Deploy from AI Studio to Cloud Run](https://codelabs.developers.google.com/deploy-from-aistudio-to-run)

## 7. 查看服務、用量與課後管理

### 在 Cloud Console 找到網站

1. 開啟 [Google Cloud Console](https://console.cloud.google.com/)
2. 切換到剛才部署的 GCP 專案
3. 搜尋並開啟 **Cloud Run**
4. 在服務清單找到本次應用程式，核對服務網址
5. 開啟服務，查看紀錄（Logs）與指標（Metrics）

Cloud Run 中的服務名稱可能由平台自動產生，請以專案與網址一起確認

### 查看 Credits 使用情況

回到 **帳單 → Credits** 查看剩餘額度、期限與適用範圍  
在帳單報表（Reports）依活動專案篩選，查看各服務的用量費用與 Credits 抵扣；帳單資料可能延遲更新

Credits 是符合條件費用的抵扣額度，請依活動規定安排課後保留或清理資源

### 取消發布

若課後暫時不需要網站上線，在 AI Studio 開啟 **Publish → Unpublish app**，依畫面確認

![Unpublish app 取消發布入口](https://raw.githubusercontent.com/sukaito94/build-with-ai-hackmd/main/images/22_cloud_run_unpublish.png)

需要保留成果時，先匯出程式與重要資料  
取消發布後，再到 Cloud Console 檢查本次建立的 Cloud Run 與其他資源；Firestore、儲存空間及建置產物的清理需分別確認

參考：[Cloud Run 服務管理](https://docs.cloud.google.com/run/docs/managing/services)

## 8. 常見狀況

| 問題 | 處理方式 |
| --- | --- |
| 發布時找不到活動專案 | 確認登入帳號，使用 Import project 匯入，核對 Project ID |
| 已領取 Credits，仍要求設定帳單 | 檢查部署專案是否連到正確帳單帳戶，請講師確認 |
| Build 要求付費 API Key 或儲值 | 先取消；建置額度與 Cloud Run Credits 分開處理 |
| 部署失敗 | 讀取發布面板錯誤，確認帳單與必要權限，將錯誤貼回 AI Studio |
| 網頁正常，Firestore 資料載入失敗 | 檢查 Firebase 專案設定、登入狀態與資料存取權限 |
| 發布後 Google 登入失敗 | 檢查 Firebase Authentication 的授權網域（Authorized domains）是否包含發布網址 |
| Preview 已更新，正式網址還是舊版 | 重新發布並重新整理正式網址 |

## 完成確認

- [ ] 已領取活動 Credits，並確認期限與適用範圍
- [ ] GCP 專案已連結正確的活動帳單帳戶
- [ ] 作品集頁面可以操作
- [ ] Firestore 資料在重新整理後仍然保留
- [ ] 發布時選擇正確的 GCP Project ID
- [ ] 已取得 Cloud Run 網址，並用無痕視窗或手機驗證
- [ ] 擁有者可編輯，其他訪客只能閱讀
- [ ] 知道如何重新發布、查看用量與取消發布

## 參考資料與圖片來源

- [Build apps in Google AI Studio](https://ai.google.dev/gemini-api/docs/aistudio-build-mode)
- [Deploying from Google AI Studio](https://ai.google.dev/gemini-api/docs/aistudio-deploying)
- [Deploy from AI Studio to Cloud Run｜Google Codelabs](https://codelabs.developers.google.com/deploy-from-aistudio-to-run)
- [What is Cloud Run](https://docs.cloud.google.com/run/docs/overview/what-is-cloud-run)
- [Manage Cloud Run services](https://docs.cloud.google.com/run/docs/managing/services)

Cloud Run 三張操作圖取自上述 Google Codelabs，依頁面標示的 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) 授權引用，原圖未修改  
Credits 與 AI Studio 操作圖片沿用原課堂教材，實際帳戶、額度與介面以本次活動為準

<!-- 原版內容來源：課堂簡報第 13–23、27–38、41、64 頁；兩段實作 Prompt 保留原文。2026-10-06 修正計費流程，加入 Cloud Run 標準部署。 -->
