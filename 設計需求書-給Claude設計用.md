# 設計需求書 — 英文金證衝刺 App（原「口說練習室」，App 名稱待定）

> **怎麼使用這份文件**：開一個新的 Claude 對話，把這份文件和 `index.html` 一起附上，然後說：
> 「請照這份設計需求書，只重新設計視覺（CSS 與必要的靜態 HTML 結構），遵守『不能動的鐵則』。」

---

## 1. 這是什麼 App

幫使用者 Polly 衝刺**多益金色證書（860 分以上，目標 900）**的個人練習網頁 App，考試日 2026/10/18。核心功能：

- 📚 **閱讀特訓**：長文計時閱讀（測 WPM）→ 選擇題 → 詳解；文章中點單字直接收進單字本
- 💬 **今日分享**：每天一題英文問答，可用麥克風說或打字，AI 批改
- 🗂️ **單字本**：收錄、自己新增、編輯（詞性＋中文）、刪除
- 📈 **我的進步**：WPM 曲線、正確率曲線、練習紀錄
- 🔥 打卡連續天數、考試倒數、激將標語（嗆人風格是特色，要保留個性）

使用情境：**手機優先**（實際上常用電腦瀏覽器開，版心 max-width 560px 置中）。

## 2. 🚫 不能動的鐵則（動了功能會壞）

1. **所有 `id` 屬性不能改名、不能刪**（JS 都靠 id 抓元素）。
2. **JS 會動態塞內容的容器**只能改外觀、不能改結構：`#shadowBody`、`#readBody`、`#dailyBody`、`#reviewBody`、`#progressBody`、`#continueBox`、`#dots`、`#motto`、`#speakNudge`、`#countdown`、`#toast`。
3. **這些 class 名稱 JS 有在用，不能改名**（樣式內容隨便改）：
   `hidden`、`on`、`done`、`sel`、`right`、`wrong`、`rec`、`blur`、`saved`、`show`、
   `card`、`btn`（含 btn-main / btn-coral / btn-ghost / btn-plain / btn-block）、
   `mode`、`opt`、`err`、`better`、`vocab-item`、`speak-mini`、`mic`、`passage`、`trans`、
   `dots`、`dot`、`stepname`、`score-row`、`score-box`、`today`、`num`、`lbl`、`tag`、`muted`、
   `loading`、`spin`、`chart`、`hist`、`field`、`timer-pill`、`w`、`back`、`row`、`en`。
4. **HTML 裡的 `onclick="..."` 全部保留原樣**。
5. **維持單一 HTML 檔**：CSS 寫在 `<style>` 內；**不可載入外部字型、外部 CSS、外部 JS、CDN、圖片檔**（圖示用 emoji 或行內 SVG）。
6. `<script>` 區塊**一個字都不要動**。
7. 語言：介面文字繁體中文、英文內容用襯線感（目前 Georgia）。

## 3. 現有設計 tokens（可以整組換掉，但請維持「海洋／靛藍」的氣質）

| Token | 目前值 | 用途 |
|---|---|---|
| --mist | #F4F8F9 | 背景 |
| --indigo | #24366B | 主色／標題／分數 |
| --teal | #177E89 | 主按鈕／強調 |
| --coral | #EF6F5A | 口說、錄音、行動呼籲 |
| --gold | #E9A93C | 連續天數、金證意象 |
| --ink | #22303C | 內文 |
| --ok / --bad | #2E9E6B / #D65A4A | 答對／答錯 |
| --line | #DFE8EC | 邊框 |
| --soft | #5B6B7A | 次要文字 |

Polly 的偏好：海洋、靛藍色系（她的桌布是海＋花束）。**金色證書＝gold 可以更放大使用**。

## 4. ✅ 可以設計的清單（隨便發揮）

### 4.1 全域
- [ ] 整體配色（維持海洋靛藍氣質、金色點綴）
- [ ] 圓角、陰影、卡片質感（目前是平面 1px 邊框，可升級成柔和陰影／玻璃感等）
- [ ] 按鈕系統四種樣式（btn-main / btn-coral / btn-ghost / btn-plain）＋按下回饋
- [ ] 字級、行高、間距節奏
- [ ] 動態效果：頁面切換、卡片 hover/active、按鈕 micro-interaction（純 CSS）
- [ ] Toast 提示的樣子（#toast）
- [ ] Loading 轉圈（.loading / .spin）

### 4.2 Header（最重要的識別區）
- [ ] indigo→teal 漸層＋底部海浪 SVG 是招牌元素，可以重畫得更精緻（多層浪、泡沫等）
- [ ] 標題／副標排版、考試倒數（#countdown）的呈現（可以做得更有壓迫感 😆）
- [ ] 連續天數火焰藥丸（.streak-pill）

### 4.3 首頁
- [ ] 激將標語卡（#motto）：目前深藍漸層，可做更有態度的設計（像大字報、貼紙感）
- [ ] 口說提醒卡（#speakNudge）：橘色左邊線，可重新設計
- [ ] 今日數據列（.today / .num / .lbl）
- [ ] 四張模式卡（.mode / .icon / .arrow）：圖示底色、hover 效果

### 4.4 閱讀特訓
- [ ] 計時藥丸（.timer-pill）
- [ ] 文章排版（.passage：目前 Georgia 17px/1.9，可調更好讀）
- [ ] 可點單字的視覺（.w:hover 與已收藏 .w.saved 目前是黃底，可更精緻）
- [ ] 選項按鈕（.opt）與作答狀態：.sel 選取、.right 答對綠、.wrong 答錯紅
- [ ] 詳解區塊的呈現

### 4.5 今日分享
- [ ] 問題卡（英文大字＋中文翻譯 .trans）
- [ ] 麥克風按鈕（.mic）與錄音中狀態（.mic.rec 目前紅色呼吸燈）
- [ ] 批改結果：分數盒（.score-box）、錯誤條目（.err）、修正版／示範（.better）、追問卡

### 4.6 單字本
- [ ] 新增單字表單（.field 輸入框）
- [ ] 單字列表（.vocab-item）＋小按鈕（.speak-mini，現在是 ✏️🗑️）
- [ ] 空狀態畫面

### 4.7 我的進步
- [ ] 統計數字列
- [ ] SVG 折線圖的配色與質感（JS 產生 polyline＋circle，只能從 CSS 與周圍容器下手）
- [ ] 練習紀錄列表（.hist）

### 4.8 加分題（選做）
- [ ] 深色模式（用 prefers-color-scheme，只靠 CSS variables 切換）
- [ ] 打卡成功的慶祝感（純 CSS 動畫）
- [ ] App 名稱的 logo 字標設計（文字＋emoji／行內 SVG）

## 5. 交付格式

- 直接給**完整的修改後 `index.html`**（`<script>` 區塊原封不動），或只給完整 `<style>` 區塊讓我整段替換。
- 附一段 100 字內的設計說明（改了什麼、為什麼）。
