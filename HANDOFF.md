# 口說練習室 — 專案交接文件（Handoff for Claude Code）

> 文件目的：讓 Claude Code 在新對話中零背景接手此專案。
> 使用者（專案負責人）：Polly — 無程式背景，負責提需求、下 prompt、驗收成果。請全程用繁體中文溝通，解釋技術時用白話比喻，一次只做一小步並讓她確認。
> 日期：2026-07-06

---

## ⚠️ 0. 重大變更（2026-07-06，Polly 拍板）

- **App 已改名為「金證衝刺」**（原「口說練習室」）。
- **視覺已換成 Claude 設計的 v2「往金色證書的航線」**（含深色模式）：設計流程＝把 `設計需求書-給Claude設計用.md` ＋ index.html 丟給另一個 Claude 對話產出。舊視覺備份在 `舊視覺備份-index.html`。設計鐵則（id/class/onclick/script 不可動）寫在需求書內，改版時務必遵守。
- **已做 iPhone 適配**：viewport-fit=cover、safe-area insets（瀏海/動態島）、apple-mobile-web-app-* meta（加入主畫面可全螢幕）、輸入框 16px 防 iOS 聚焦縮放。Polly 的用法：檔案傳到 iPhone 後從 Safari 開啟→加入主畫面（正式 App 化仍走 Phase 3 Capacitor）。

- **核心從「口說跟讀」改為「閱讀特訓」**。原因：Web Speech TTS 音質 Polly 無法接受（多種修法試過仍嫌卡／難聽），且多益 L&R 本來就考閱讀；Polly 自認口說可自行安排。
- 跟讀六步驟的程式**封存不刪**（index.html 內 SH 系列函式，首頁入口已移除），日後有好的語音方案（Edge 自然語音／Capacitor 原生 TTS）可一鍵接回。
- 新核心「閱讀特訓」：長文 300–400 字（金證難度、Part 7 題型含雙篇）＋**閱讀計時（WPM）**（因 Polly 自述閱讀偏慢）＋ 4–5 題選擇題附繁中詳解＋**點文中任意字即收進單字本**（文章附 glossary，點到表列字自動帶中文意思）。
- 題庫在 index.html 的 READING_BANK（目前 3 篇假資料）；接真 AI 後改為 AI 產生長文＋題目。

## 1. 專案一句話

一個幫 Polly 考到多益**金色證書（860 以上，目標拚 900）**的英文練習 App（起點約 670–700），核心是 **AI 批改的口說練習（Shadowing 跟讀法）**，搭配單字間隔重複、成績追蹤與每日打卡。先做網頁版（HTML），之後比照她的另一個專案「烘焙小天地」，用 **Capacitor 轉成 iOS App**。

---

## 2. 使用者輪廓與需求來源（很重要，功能取捨都源自這裡）

| 項目 | 內容 | 對設計的影響 |
|---|---|---|
| 英文程度 | 多益起點約 670–700，**目標金色證書 860↑（拚 900）** | 素材難度抓在 **800–860 踮腳尖區間**，隨程度自適應往上（80% 理解度原則） |
| 考試日期 | **2026-10-18 多益**（首頁 header 有倒數，index.html 的 EXAM_DATE） | 倒數天數天天可見；改期只需改該常數 |
| 賭注動機 | 跟媽媽打賭：**沒考到就給媽媽三萬元旅費**；另有「要用口說超越某位靠英文上更好大學的人」的心結 | 兩者都做成激將標語（MOTTOS 前三句，Polly 親自指定，語氣要嗆才有效） |
| 發音 | 公認不錯 | **不做發音評分**，批改重點放在流暢度、用字、文法 |
| 最在意 | 能不能流暢講出來、用字文法對不對、AI 給回饋跟範例 | 批改回傳必含：修正版 + 更道地範例 |
| 想要的功能（她親口點名） | 口說優先、背單字、成績變化紀錄、**每天提醒**、**「今日分享」模式（用英文說今天在幹嘛）**、**激將法標語（嗆到有動力那種，首頁隨機輪播，已做，見 index.html 的 MOTTOS）** | 全部列入規格，見 §4 |
| 情境偏好 | TOEIC 題型／科技公司職場英文／歐洲旅遊 | 素材產生器的三個 preset（＋今日分享算第四種自由模式） |
| 工作背景 | 台灣光學科技公司（永視科技）工讀生、輔大化學系大一 | 職場素材可帶科技公司元素 |

**每日提醒的技術現實（已跟 Polly 說明並同意）**：網頁版做不到關閉後推播，只能做「開啟時顯示今日進度／連續天數」。真正的推播要等 Phase 3 上 iOS（Capacitor Local Notifications）。

---

## 3. 研究依據（NotebookLM 整理結果 → 已轉成規格）

Polly 依老闆要求先用 NotebookLM 研究了主流學習法，以下是**必須遵守的規格**，不是參考：

### 3.1 Shadowing 六步驟（口說模式主流程）
1. **盲聽**（不看稿聽全文，感受節奏）
2. **理解**（看逐字稿＋生字中文解釋，需 100% 看懂才往下）
3. **看稿同步跟讀**（原速，可切慢速）
4. **無稿盲跟**（藏稿播放，延遲 0.5–1 秒跟讀）
5. **復述**（脫稿用自己的話講出大意 → 送 AI 批改）※原研究還有「錄音對比」，v1 簡化併入此步
6. **內化**（看批改結果、存生字、完成打卡）

### 3.2 素材與時長規則
- 素材長度：**10–30 秒**（約 60–80 個英文字）
- **同一段素材連續練 3–5 天**才換新 → App 要記住當前素材與已練天數，回訪時預設「繼續練這段（第 N 天）」，換新素材是次要選項
- 單次練習 10–15 分鐘；累計達 30 分鐘要跳休息提醒
- 每日目標預設：**2–3 回、每回 5–10 分鐘**（成績頁可調）

### 3.3 間隔重複（SRS）時間表 — 單字複習的排程引擎
四個作答按鈕與間隔：

| 按鈕 | 意義 | 下次出現 |
|---|---|---|
| 忘了 | 完全想不起來 | **10 分鐘內**（同場複習佇列尾端）；已有間隔者新間隔 = 原間隔 × 0.6 |
| 勉強 | 很吃力才想起 | 1–3 天（實作：max(1, 原間隔 × 1.2)） |
| 記得 | 想一下想起來 | 4–7 天（實作：首次 4 天，之後 × 2.5） |
| 很熟 | 秒答 | 14 天以上（實作：首次 14 天，之後 × 3.5） |

### 3.4 台灣學習者常錯清單（AI 批改的重點檢查表，寫進批改 prompt）
- 直譯搭配詞：do a decision→make a decision、eat medicine→take medicine、open/close the light→turn on/off、wait me→wait for me、live in a hotel（暫住）→stay
- I very like it → I really like it / I love it
- 濫用 Of course（帶諷刺感）→ Sure / Definitely
- How to say? → How do you say...?
- he/she、his/her 口說混用
- 冠詞漏用（I ate apple）與贅用（gained the weight）
- 不可數名詞加 s（informations、advices）；可數複數漏 s（three dog）
- There have... → There are...；雙動詞（There are many people think...）

### 3.5 難度自適應
- 材料理解度維持 **80–90% 正確率區間**：連續高於 90% 升難、低於 80% 降難（Krashen i+1）

### 3.6 打卡與習慣機制
- **連續天數（Streak）**＋每月 1 張「請假卡」（Streak Freeze，斷一天不歸零）
- 完成任一回練習即算當日打卡
- 微獎勵：完成時的慶祝動畫／音效可加分但非必要

---

## 4. 功能規格（四大模式）

### 4.1 跟讀練習（Shadowing）— 核心功能
- 進入先選情境：TOEIC / 職場 / 歐洲旅遊
- 若有進行中素材（<5 天）→ 優先顯示「繼續練這段」
- 依 §3.1 六步驟精靈式進行，頂部有步驟進度條
- 步驟 5 復述輸入：**麥克風語音轉文字（主）＋打字（備援）**
- AI 批改回傳（JSON）：流暢度 1–5、用字文法 1–5、錯誤列表（原句→修正→類型→白話解釋）、修正版全文、更道地範例、一句鼓勵
- 批改後的修正版與範例要能**點擊用 TTS 唸出來**（供跟讀）
- 素材中的生字自動寫入單字庫開始跑 SRS

### 4.2 今日分享（Daily Talk）— Polly 點名要的
- AI 出一個關於「今天」的問題（輪換：今天做了什麼／工作學到什麼／吃了什麼／心情如何…）
- 使用者用說的或打的回答（英文）
- AI 批改：同 4.1 的批改格式（對照台灣常錯清單），並回一個 follow-up 追問（v1 可單輪，追問顯示但不強制回答）
- 此模式素材天天不同，不套用「連練 3–5 天」規則

### 4.3 單字複習（SRS）
- 顯示今日到期卡片數；卡片：英文字 → 點擊翻面看中文＋例句（例句可 TTS）→ 四鍵作答（§3.3）
- 生字來源：跟讀素材生字＋批改中被修正的用字（AI 批改回傳可含 suggested_vocab）
- 全部複習完顯示完成畫面與明日預告數

### 4.4 我的進步
- 連續天數、累計回數、單字庫量
- 成績曲線：每回練習分數 =（流暢度＋用字文法）× 10（滿分 100），SVG 折線圖近 20 筆
- 練習歷史列表（日期／模式／分數）
- 每日目標設定（預設每天 2 回）

---

## 5. 技術規格

### 5.1 分階段路線圖
| 階段 | 內容 | 產出 |
|---|---|---|
| **Phase 1（現在）** | 網頁版：跟讀＋今日分享＋批改，資料存瀏覽器 | 單一 HTML 或小型專案資料夾 |
| **Phase 2** | 單字 SRS＋進步頁＋打卡完整化 | 同上迭代 |
| **Phase 3** | ✅ 已改走 **PWA 路線**（2026-07-06，與烘焙小天地實際做法相同，非 Xcode）：manifest.json＋sw.js＋icon-512/192/180.png 已就緒，index.html 已掛 manifest、apple-touch-icon 與 SW 註冊 | **已上線：https://polly1228.github.io/toeic/**（GitHub 倉庫 Polly1228/toeic，Pages 從 main /(root) 部署）。更新流程＝改完本機檔案 → 到倉庫網頁重新上傳蓋掉同名檔 → **記得把 sw.js 的 CACHE 版本號 +1（gold-sprint-v1 → v2…）**，否則舊快取不會更新。上架方式＝把整個資料夾放上 HTTPS 空間 → iPhone Safari 開啟 →「加入主畫面」。圖示由 PowerShell System.Drawing 生成（金牌「金」字＋海浪）。推播提醒 PWA 仍受限，若未來要推播再評估 Capacitor |

### 5.2 AI 呼叫 —— ⚠️ 最重要的技術決策點，請先跟 Polly 討論
此 App 核心功能（生素材、批改）都要呼叫 Claude API。**與烘焙小天地不同，本專案無法純離線。**在 Claude Code 建置的獨立網頁/iOS App 中呼叫 Anthropic API 需要 API key，選項：

1. **（建議 v1）App 內設定頁讓使用者貼上自己的 API key**，存在本機（localStorage/Capacitor Preferences），呼叫 `https://api.anthropic.com/v1/messages`，並加上 `anthropic-dangerous-direct-browser-access: true` header（個人自用可接受；**絕不可把 key 寫死在程式碼或公開發佈**）
2. （之後）架一個小後端代理保管 key —— Phase 3 若要給別人用再做
- key 需 Polly 向公司或老闆申請 Anthropic API key；帳務歸公司。請提醒她先問老闆。
- 模型建議 `claude-sonnet-4-6`（品質/成本平衡），max_tokens 1500 內
- 所有 AI 回傳一律要求「只回 JSON、不要前言不要 markdown 圍欄」，前端做 `replace(/```json|```/g,'')` 清理＋try/catch，失敗顯示「AI 沒回好，再試一次」按鈕

### 5.3 AI Prompt 規格（兩支）

**A. 產生素材**（輸入：情境、目前難度級別 1–5）
要求回傳 JSON：
```json
{
  "title": "英文標題",
  "text": "60-80 字英文短文或對話",
  "translation": "全文中文翻譯",
  "vocab": [{ "word": "", "meaning": "中文意思", "example": "例句" }]  // 4-6 個
}
```
Prompt 要點：TOEIC **800–860 金證學習區**（起點者可 730 起步，隨 level 往上）、指定情境、口語可朗讀的文體、vocab 挑 **860↑ 金色證書常考字（含商用慣用語、phrasal verbs）**。

**B. 批改復述／今日分享**（輸入：原素材全文或問題、使用者的回答）
要求回傳 JSON：
```json
{
  "fluency": 1-5,
  "accuracy": 1-5,
  "errors": [{ "original": "", "corrected": "", "type": "搭配詞/冠詞/時態/單複數/用字", "explain": "繁中白話解釋" }],
  "improved": "使用者回答的修正版（保留其原意與口吻）",
  "native_example": "更道地的示範答案",
  "encouragement": "一句具體的繁中鼓勵",
  "suggested_vocab": [{ "word": "", "meaning": "", "example": "" }],
  "follow_up": "追問（僅今日分享模式）"
}
```
Prompt 要點：**必須對照 §3.4 台灣常錯清單逐項檢查**；解釋一律繁體中文；語氣鼓勵但誠實；不評發音。

### 5.4 語音
- TTS：Web Speech API `speechSynthesis`，選 `en-US` voice，提供 0.8x 慢速鈕；注意 `getVoices()` 非同步載入
- STT：`webkitSpeechRecognition`（Chrome 可用），`lang='en-US'`；**偵測不支援時自動隱藏麥克風鈕、只留打字**——這是必備 fallback，不能省
- Phase 3 iOS 注意：WKWebView 的 Web Speech 支援不完整，屆時改用 Capacitor 社群外掛（如 @capacitor-community/speech-recognition、text-to-speech），此為已知的移植工作項

### 5.5 資料模型（本機儲存，單一 key 存 JSON 整包）
```json
{
  "streak": { "count": 0, "lastDate": "YYYY-MM-DD", "freezeLeftThisMonth": 1 },
  "sessions": [{ "date": "", "mode": "shadow|daily", "score": 0-100 }],
  "vocab": [{ "word": "", "meaning": "", "example": "", "due": "ISO時間", "intervalDays": 0, "addedDate": "" }],
  "currentMaterial": { "scenario": "", "title": "", "text": "", "translation": "", "vocab": [], "daysUsed": 1, "lastUsedDate": "", "level": 3 },
  "settings": { "dailyGoal": 2, "apiKey": "" },
  "recentAccuracy": [用來做難度自適應的近 5 回正確率]
}
```
- 每次寫入用 try/catch；壞檔要能重置不炸掉
- Phase 3 換 Capacitor Preferences，資料結構不變

### 5.6 設計系統（已定案，沿用即可）
Polly 喜歡海洋／靛藍色系（她的桌布專案也是海＋花束）。已建立的 tokens：

| Token | 值 | 用途 |
|---|---|---|
| --mist | #F4F8F9 | 背景 |
| --indigo | #24366B | 主色／標題／分數 |
| --teal | #177E89 | 主按鈕／強調 |
| --coral | #EF6F5A | 口說／錄音相關 |
| --gold | #E9A93C | 連續天數火焰 |
| --ink | #22303C | 內文 |
| --ok / --bad | #2E9E6B / #D65A4A | 批改對錯 |

- Header：indigo→teal 漸層＋底部海浪 SVG（signature 元素）
- 英文內文用襯線字（Georgia）、中文用系統黑體；不載入外部字型
- 手機優先（max-width 560px 置中）
- **已有一個 UI 骨架檔**：`口說練習室.html`（只有 HTML 結構＋完整 CSS，**尚無任何 JS 邏輯**），可直接沿用其畫面結構：首頁四卡、情境選擇、六步驟精靈、今日分享、SRS 卡片、進步頁

### 5.7 目錄結構建議（Phase 1）
```
speaking-app/
├── index.html        （可先單檔起步，之後再拆）
├── css/style.css
├── js/
│   ├── app.js        （畫面切換、狀態）
│   ├── ai.js         （API 呼叫、prompt、JSON 清理）
│   ├── speech.js     （TTS/STT 封裝＋fallback）
│   └── srs.js        （間隔重複排程）
└── HANDOFF.md        （本文件）
```

---

## 6. 驗收清單（Polly 逐項打勾）

**Phase 1**
- [ ] 首頁看得到：連續天數、今日已練回數、待複習單字數
- [ ] 跟讀六步驟能完整走完一輪（三種情境都試）
- [ ] 素材第二天回來，預設是「繼續練這段（第 2 天）」
- [ ] 復述可以用麥克風講（Chrome），不支援的瀏覽器自動變打字
- [ ] 批改結果有：兩個分數、逐條錯誤（含中文解釋）、修正版、道地範例，且範例可點擊朗讀
- [ ] 今日分享：AI 會問今天的事，批改格式同上、多一個追問
- [ ] 練完一回，連續天數＋1（同日多回不重複加）
- [ ] AI 失敗時有「重試」而不是白畫面
- [ ] 沒填 API key 時，有清楚的設定引導頁

**Phase 2**
- [ ] 單字四鍵作答，間隔符合 §3.3（可用「改系統時間」或測試模式驗證）
- [ ] 成績曲線正確累積
- [ ] 連續 90%+ 正確率後素材變難；低於 80% 變簡單

---

## 7. 給 Claude Code 的協作方式（請務必遵守）

1. Polly 不寫程式：每完成一小塊就**用白話說明做了什麼、請她打開瀏覽器驗收**，通過再往下。
2. 一次一個功能，順序建議：畫面切換 → 素材產生 → 六步驟流程 → 批改 → 今日分享 → 打卡 →（Phase 2）SRS → 進步頁。
3. 開工第一件事：跟她確認 **API key 怎麼取得**（§5.2，需要問老闆），沒有 key 前可先用「假資料模式」把流程做出來讓她驗收 UI。
4. 所有給她看的訊息、按鈕、錯誤提示一律繁體中文。
5. 不要一次重構或大改她已驗收過的畫面；要改先講原因。

---

## 8. 背景補充（可略讀）

- 此專案是 Polly 在永視科技的工讀實作題之一，老闆指定流程：先用 NotebookLM 做學習法研究 → 再落地成 App。§3 全部來自該研究，向老闆簡報時可強調「功能設計有研究依據」。
- 她已有成功前例：烘焙小天地（HTML → Claude Code → Capacitor iOS 路線相同）、倉儲管理系統（含 handoff 文件交接經驗）。
- 未來如果老闆問「能不能給其他同事用」：那時才需要考慮後端代理 API key 與帳號系統，v1 不用。
