---
name: taiwan-stock
description: 產生台股「未來7天除權息」觀察報告。抓取除權息清單、對優先個股做深入查證與評分、輸出固定格式的深色系HTML報告。當使用者要求「除權息報告」「填息觀察」「/taiwan-stock」或每日排程觸發時使用。
---

# 台股除權息填息觀察報告

產出一份聚焦「未來7天內除權息個股」的填息觀察報告，格式固定為深色系 HTML，檔名為 `MMDD_result.html`（MM/DD 為報告產出當天日期，例如 9/7 產出 → `0907_result.html`，存放於 repo 根目錄）。

## 執行流程（依序進行，不可跳步）

### 1. 抓取除權息清單
- 優先來源：HiStock（如 https://histock.tw/stock/exright.aspx 或其個股頁面）。若 HiStock 當日資料不足或無法取得，可輔以其他公開資訊來源（如證交所、公開資訊觀測站、Goodinfo）補齊。
- 範圍：以報告產出日為起點，未來 7 天內的除權息個股（含除權、除息、權息）。
- 若近 7 天內個股數量過少（例如少於 5 檔），可額外延伸收錄 1~3 天以利參考，但必須在標題 `meta` 欄位與該個股卡片上明確標註「超出7天範圍」等字樣，不可默默混入當作 7 天內資料。
- 記錄每檔基本資訊：除息/除權日、股票代號、名稱、現金股利（或股票股利）、殖利率。

### 2. 篩選優先查證個股
從清單中挑出值得深入查證的個股，優先順序：
- 中大型股（市值、知名度較高，資訊較充分、較具參考意義）
- 消息面明確者（近期有法說、財報、產業消息、法人動向等可查證的具體事件）
- 殖利率極端者（過高或過低，特別是殖利率 >8% 需優先查證，見第 6 點紅線）

不需要、也不應該對清單上每一檔都做深入查證——查證數量取決於實際可取得資訊與時間，寧可查得少但確實，不可為了湊數量而查得淺。

### 3. 深入查證（僅對第 2 步篩出的個股）
針對每一檔優先個股，盡量查證以下面向：
- 近期股價動能（近期漲跌幅、是否接近高/低點、成交量變化）
- 歷史填息紀錄（過去除息後填息所需天數、是否曾貼息）
- 財報評分／體質（EPS 趨勢、財報評分工具數據、與同業比較）
- 法人籌碼（外資/投信買賣超、持股變化）
- 重大消息（法說會內容、產業趨勢、公司公告、政策影響等）

**誠實原則**：若時間或資料來源限制，無法完成上述查證中的關鵽項目，**不可編造或推測數據來填補**。凡查證不足以支撐具體評分與理由的個股，一律移入第 4 點的「資料有限（未評分）」區塊，只列基本除息資訊。

### 4. 資料有限（未評分）區塊
- 適用對象：清單中未被列為優先查證、或查證時間不足以完成第 3 步查證的個股。
- 只呈現基本除息資訊表格：日期、代號、名稱、股利、殖利率。
- 不得附加分析文字、不得評分、不得暗示任何買賣建議。

### 5. 對已深入查證個股評分
對完成第 3 步查證的個股，給出 **-3 ~ +3** 的整數評分：
- `+3 ~ +1`：填息(權)機率偏高、風險相對可控
- `0`：機會與風險相當，或關鍵資訊仍有缺口
- `-1 ~ -3`：填息(權)機率偏低或有明確風險，分數越低風險越高

每檔須具體寫出：
- **理由**（`reasons`）：根據第 3 步查得的具體事實（動能、籌碼、財報、消息），言之有物，避免空泛套話。
- **風險**（`risk`）：至少一項具體風險點，包含但不限於：短線動能與基本面背離、評價過高、產業/政策不確定性、殖利率異常（見第 6 點）。

評分與理由必須可回溯到第 3 步查得的具體資訊，不可先射箭再畫靶。

### 6. 品質紅線（不可違反）
- **不可編造數據**：任何分數、理由、風險描述都必須有查證依據；查不到就歸類「資料有限」，不可硬湊評分。
- **不可省略免責聲明**：輸出的 HTML 必須包含完整 footer 免責聲明（見下方模板），包含分數說明與紅漲綠跌配色提醒。
- **殖利率利率陷阱提示**：殖利率 >8% 的個股，若被列入深入分析區塊，`risk` 欄位必須主動提示「利率陷阱」疑慮（例如：填息前一次性大額配息是否反映一次性業外收益、除息後貼息機率偏高、是否為降低股價的財務操作等），不可只當作正面訊號帶過。若查證時間不足以判斷，該股應歸入「資料有限」而非硬評分。

## 輸出格式：固定 HTML 模板

檔名：`MMDD_result.html`（存於 repo 根目錄，MM/DD 為報告當天日期）。

必須沿用以下結構與 class 命名（可參考 repo 內既有的 `0901_result.html` / `0902_result.html` / `0905_result.html` / `0907_result.html` 作為範例），核心規則：

- 深色系配色：背景 `#12161C`、卡片面 `#1A2028`、邊線 `#2A323D`、主文字 `#E9EBEF`、次要文字 `#8A93A3`、金色點綴 `#D4A24C`。
- **配色慣例**：台股採「紅漲綠跌」，正分（`+`）用紅色（`--gain: #E5484D`），負分（`-`）用綠色（`--loss: #2FA875`），與美股慣例相反，需在 footer 註明避免誤讀。
- 字型：標題用 `Noto Serif TC`，內文用 `Noto Sans TC`，代號/數字用 `IBM Plex Mono`。
- 版面結構（由上而下）：
  1. `header`：eyebrow 標籤（如 `TAIWAN DIVIDEND · FILL-GAP WATCH`）、標題 `h1`（如「本週除息填息觀察」）、`meta`（資料日期、涵蓋範圍，若有延伸超出7天需在此註明）。
  2. `summary`：統計列，顯示「已深入分析 N 檔」「資料有限(未評分) N 檔」「可留意 N 檔」等統計數字。
  3. `section-label` + `section-sub`：「個股｜已深入分析」區塊說明。
  4. 每檔一張 `.card`：左側 `.score`（圓形評分徽章，正分 class `pos`、負分 class `neg`、0 分 class `zero`）；右側 `.body` 內含：
     - `.row1`：代號（`.ticker`）、名稱（`.name`）、標籤（`.tag`，如「個股」「超出7天範圍」）
     - `.row2`：除息日、股利、殖利率
     - `.reasons`：查證理由
     - `.risk`：風險提示（含 `<b>風險</b>：` 前綴，殖利率>8%須含利率陷阱提示）
  5. `section-label`「個股｜資料有限(未評分)」+ `section-sub` 說明。
  6. `.lite-table`：資料有限個股的基本資訊表格（日期／代號／名稱／股利／殖利率）。
  7. `footer`：完整免責聲明（見下方固定文字，可依當期實際狀況微調但不可省略核心內容）。

### 完整 HTML 模板（複製後填入當期資料）

```html
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<title>台股除息填息觀察</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Noto+Serif+TC:wght@600;700&family=Noto+Sans+TC:wght@400;500;700&family=IBM+Plex+Mono:wght@500;600&display=swap');

  :root{
    --bg: #12161C;
    --surface: #1A2028;
    --line: #2A323D;
    --text: #E9EBEF;
    --text-dim: #8A93A3;
    --gain: #E5484D;
    --loss: #2FA875;
    --neutral: #8A93A3;
    --gold: #D4A24C;
  }

  * { box-sizing: border-box; }
  body{
    margin:0; padding: 32px 16px 64px;
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans TC', sans-serif;
  }
  .wrap{ max-width: 720px; margin: 0 auto; }

  header{ margin-bottom: 28px; }
  header .eyebrow{
    font-family: 'IBM Plex Mono', monospace;
    font-size: 12px; color: var(--gold); letter-spacing: 0.04em;
  }
  header h1{
    font-family: 'Noto Serif TC', serif;
    font-size: 26px; font-weight: 700; margin: 6px 0 4px;
  }
  header .meta{ font-size: 13px; color: var(--text-dim); }

  .summary{
    display:flex; gap: 20px; margin: 18px 0 28px; flex-wrap: wrap;
    padding: 14px 16px; background: var(--surface);
    border: 1px solid var(--line); border-radius: 4px;
  }
  .summary .item{ font-size: 13px; color: var(--text-dim); }
  .summary .item b{ color: var(--text); font-family:'IBM Plex Mono',monospace; font-weight:600; }

  .section-label{
    font-family: 'Noto Serif TC', serif; font-size: 15px; font-weight: 700;
    color: var(--text); margin: 28px 0 10px; padding-bottom: 8px;
    border-bottom: 1px solid var(--line);
  }
  .section-sub{
    font-size: 12px; color: var(--text-dim); margin: -4px 0 14px;
  }

  .card{
    display: flex; gap: 16px;
    padding: 16px 4px;
    border-bottom: 1px solid var(--line);
    align-items: flex-start;
  }
  .card:last-child{ border-bottom: none; }

  .score{
    flex: 0 0 44px; height: 44px; border-radius: 50%;
    display:flex; align-items:center; justify-content:center;
    font-family: 'IBM Plex Mono', monospace; font-size: 17px; font-weight: 600;
    border: 1.5px solid currentColor;
  }
  .score.pos{ color: var(--gain); }
  .score.neg{ color: var(--loss); }
  .score.zero{ color: var(--neutral); }

  .body{ flex: 1; min-width: 0; }
  .row1{ display:flex; align-items: baseline; gap: 8px; flex-wrap: wrap; }
  .ticker{ font-family:'IBM Plex Mono',monospace; font-size: 13px; color: var(--text-dim); }
  .name{ font-size: 16px; font-weight: 700; }
  .tag{
    font-size: 11px; padding: 1px 7px; border-radius: 3px;
    border: 1px solid var(--line); color: var(--text-dim);
  }
  .row2{ font-size: 12.5px; color: var(--text-dim); margin-top: 3px; }
  .reasons{ font-size: 13.5px; margin-top: 8px; line-height: 1.55; }
  .risk{ font-size: 12.5px; color: var(--text-dim); margin-top: 6px; }
  .risk b{ color: var(--gold); font-weight: 500; }

  .lite-table{ width: 100%; border-collapse: collapse; font-size: 13px; }
  .lite-table th{
    text-align: left; font-weight: 500; color: var(--text-dim);
    font-size: 11.5px; padding: 6px 8px; border-bottom: 1px solid var(--line);
  }
  .lite-table td{
    padding: 9px 8px; border-bottom: 1px solid var(--line); color: var(--text);
  }
  .lite-table td.ticker-cell{ font-family:'IBM Plex Mono',monospace; color: var(--text-dim); }
  .lite-table tr:last-child td{ border-bottom: none; }

  footer{
    margin-top: 36px; padding-top: 16px; border-top: 1px solid var(--line);
    font-size: 11.5px; color: var(--text-dim); line-height: 1.6;
  }
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div class="eyebrow">TAIWAN DIVIDEND · FILL-GAP WATCH</div>
    <h1>本週除息填息觀察</h1>
    <div class="meta">資料日期：YYYY/MM/DD　·　範圍：YYYY/MM/DD ～ YYYY/MM/DD</div>
  </header>

  <div class="summary">
    <div class="item">已深入分析　<b>N</b> 檔</div>
    <div class="item">資料有限(未評分)　<b>N</b> 檔</div>
    <div class="item">可留意　<b>N</b> 檔</div>
  </div>

  <div class="section-label">個股｜已深入分析</div>
  <div class="section-sub">已查證近期股價動能、法人籌碼、基本面消息或歷史填息紀錄</div>

  <!-- 每檔複製一份 .card，評分決定 score class：pos(+分)/neg(-分)/zero(0分) -->
  <div class="card">
    <div class="score pos">+1</div>
    <div class="body">
      <div class="row1"><span class="ticker">代號</span><span class="name">名稱</span><span class="tag">個股</span></div>
      <div class="row2">除息日 M/D　·　現金股利 X元　·　殖利率 X.XX%</div>
      <div class="reasons">具體查證理由...</div>
      <div class="risk"><b>風險</b>：具體風險描述...</div>
    </div>
  </div>

  <div class="section-label">個股｜資料有限(未評分)</div>
  <div class="section-sub">受限查證時間，僅列除息基本資訊，未能取得足夠的股價動能/籌碼/歷史填息數據</div>

  <table class="lite-table">
    <tr><th>日期</th><th>代號</th><th>名稱</th><th>股利</th><th>殖利率</th></tr>
    <!-- 每檔一列 <tr><td>M/D</td><td class="ticker-cell">代號</td><td>名稱</td><td>股利</td><td>殖利率</td></tr> -->
  </table>

  <footer>
    ⚠️ 以上分析基於公開資訊與歷史統計，僅供參考，不構成投資建議。填息與否受大盤、產業、公司基本面等多重因素影響，
    過去填息紀錄不保證未來表現，投資人應自行判斷並承擔風險。<br>
    分數說明：+3~+1 代表填息(權)機率偏高、風險相對可控；0 代表機會與風險相當或資料不足；-1~-3 代表填息(權)機率偏低或有明確風險，分數越低風險越高。
    台股慣例採「紅漲綠跌」，本頁配色沿用此慣例（正分＝紅、負分＝綠），與美股慣例相反，請留意。<br>
    「資料有限」區塊之個股因查證時間限制，僅列示除息基本資訊，未經過近期股價動能、籌碼與歷史填息紀錄的完整查證，請自行進一步確認後再做判斷。
  </footer>

</div>
</body>
</html>
```

## 完成後的檢查清單
- [ ] 檔名為 `MMDD_result.html`（今天日期），放在 repo 根目錄
- [ ] `summary` 統計數字與實際卡片/表格列數一致
- [ ] 每檔「已深入分析」都有具體、可回溯查證來源的 `reasons` 與 `risk`，無編造內容
- [ ] 殖利率 >8% 的個股，若列入深入分析，`risk` 已包含利率陷阱提示；若查證不足則歸入資料有限
- [ ] 「資料有限」表格只有基本資訊，無評分、無分析文字
- [ ] footer 免責聲明完整（含分數說明、紅漲綠跌配色提醒）
- [ ] 若收錄超出7天範圍的個股，已在 `meta` 與對應卡片 `.tag` 標註
