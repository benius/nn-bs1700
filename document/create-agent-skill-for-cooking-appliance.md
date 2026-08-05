# 從烹調設備使用手冊建立專屬 Agent Skill

本指南說明如何把設備商提供的使用手冊，整理成 CulinaForge 可使用的烹調設備 Agent Skill。完成後，agent 應能查詢該設備的操作、模式、功率或火力、配件、容器、時間上限、安全限制、清潔與故障處理，也能在多設備食譜中只負責自己的設備步驟。

最重要的規則只有一條：**一個 Skill 只包含一台烹調設備。** 即使同一本手冊同時說明多個型號，也要為每個目標型號分別建立 Skill，並從各自的內嵌參考資料中移除其他型號的專屬內容。

## 目錄

- [開始前先準備什麼](#開始前先準備什麼)
- [決定 Skill 名稱與設備邊界](#決定-skill-名稱與設備邊界)
- [整理使用手冊](#整理使用手冊)
- [規劃 Skill 目錄](#規劃-skill-目錄)
- [初始化 Skill](#初始化-skill)
- [撰寫 references](#撰寫-references)
- [撰寫 SKILL.md](#撰寫-skillmd)
- [建立 agents/openai.yaml](#建立-agentsopenaiyaml)
- [接上 CulinaForge 食譜流程](#接上-culinaforge-食譜流程)
- [驗證與前向測試](#驗證與前向測試)
- [可直接使用的建立提示](#可直接使用的建立提示)
- [完成檢查表](#完成檢查表)

## 開始前先準備什麼

至少準備以下資料：

1. 設備品牌與完整型號。
2. 設備商提供的使用手冊、操作指南或食譜手冊，以及版本日期或文件編號。
3. 手冊適用的型號範圍；確認目標型號是否與其他型號共用同一本手冊。
4. 希望 Skill 回答的實際問題，例如操作、料理火力、配件、故障碼或清潔。
5. 手冊檔案的使用與散布條款。

使用從官方管道取得的手冊。若把 PDF、掃描圖片或 OCR 結果複製到工作區供 agent 整理，暫存檔一律放在 `tmp/`。不要把沒有授權重新散布的原始手冊、整頁掃描或圖片直接提交到 repository；依權利條款保存可使用的摘要、改寫資料與來源頁碼。

先列出 3～5 個完成後要能回答的問題。這些問題會決定 Skill 的觸發描述與參考檔分法，例如：

- 「這台烤箱用純蒸氣處理冷藏魚時，配件與層位怎麼放？」
- 「右爐開 Booster 時，左爐還能設定幾段火力？」
- 「顯示某個錯誤碼時，要先停機還是可以繼續使用？」
- 「如何清潔水路或玻璃面板？」

## 決定 Skill 名稱與設備邊界

Skill 名稱建議採：

```text
cooking-with-<model>
```

名稱只使用小寫英文字母、數字與連字號，長度少於 64 個字元；目錄名稱必須和 `SKILL.md` frontmatter 的 `name` 完全相同。例如：

```text
cooking-with-nn-bs1700
cooking-with-rb-2232h
```

先寫一段明確的設備邊界：

- 只回答哪個品牌與型號。
- 哪些模式、功能、配件或爐口屬於這台設備。
- 手冊同時涵蓋其他型號時，只保留目標型號欄位及確定共通、可直接適用的規則。
- 不回答其他型號的比較，也不把其他設備的時間、火力或功率換算進來。
- 如果要支援另一台設備，另建一個 Skill。

「來源手冊同時包含多個型號」不代表 Skill 可以包含多台設備。可以在來源說明中交代原手冊範圍，但最終 `references/` 不應保留其他型號的規格列、專屬按鍵行為、錯誤處置或安裝尺寸。

## 整理使用手冊

### 1. 建立來源清單

為每份文件記錄：

| 欄位 | 內容 |
| --- | --- |
| 文件名稱 | 設備商公布的完整名稱 |
| 目標型號 | 這個 Skill 唯一支援的型號 |
| 版本 | 日期、版次或文件編號 |
| 頁碼範圍 | 實際使用的章節與頁數 |
| 資料性質 | 官方操作手冊、官方食譜手冊、快速指南等 |
| 使用限制 | 是否可保存、改寫、引用或重新散布 |

若有多份官方文件，先定義來源優先序。通常先採型號完全相符且版本較新的正式使用手冊，再用官方補充指南或食譜手冊補缺；不要把衝突值平均。無法確認哪份較新或較適用時，保留衝突並要求以設備畫面或最新版官方文件為準。

### 2. 擷取文字並核對版面

文字型 PDF 可以先擷取文字；掃描型 PDF 需要 OCR。無論使用哪種方法，都要回看原頁面核對：

- 數字、小數點、單位與正負號。
- 表格欄位是否錯位。
- 型號差異、左／右爐或上／下層是否被混在一起。
- 圖示旁的警告、例外與腳註。
- 模式名稱、面板顯示碼與按鍵順序。

OCR 結果只適合當初稿。功率、溫度、時間、尺寸、錯誤碼與安全警告都要逐項核對原頁面。

### 3. 只保留可操作資訊

把手冊內容整理成 agent 能查找與執行的資料，而不是逐頁複製。優先保留：

- 開關機、模式選擇與面板操作。
- 功率、溫度、火力、時間範圍與自動行為。
- 配件、容器、鍋具、盤面、層位與預熱要求。
- 份量、尺寸、厚度、液體量或其他適用條件。
- 安全警告、禁止事項、食品完成判定與包裝優先規則。
- 故障碼、立即處置、清潔、保養、安裝與售後服務。

廣告文字、品牌故事、重複敘述、無法解析的圖片裝飾和與目標型號無關的資料不必放進 Skill。

## 規劃 Skill 目錄

把 Skill 放在 CulinaForge 的 `.agents/skills/`：

```text
.agents/skills/cooking-with-<model>/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── operation-and-safety.md
    ├── modes-and-cooking.md
    ├── specifications-and-cookware.md
    ├── troubleshooting-and-cleaning.md
    └── installation-and-service.md
```

這只是常見分法。只建立實際需要的檔案，不要為了湊齊目錄而新增空白文件，也不要在 Skill 裡建立 README、安裝指南、變更記錄或其他使用者文件。

`SKILL.md` 保留每次都需要的來源邊界、查找流程、料理判斷與最後檢查；詳細表格、數字和長篇操作則放到 `references/`。若參考檔超過約 100 行，在檔案開頭加入目錄，方便 agent 先判斷要讀哪一節。

## 初始化 Skill

建立新 Skill 時使用 `skill-creator` 提供的 `init_skill.py`，不要手動拼出不完整的空目錄。從 CulinaForge repository 根目錄執行：

```bash
python3 <skill-creator 根目錄>/scripts/init_skill.py \
  cooking-with-<model> \
  --path .agents/skills \
  --resources references \
  --interface 'display_name=<品牌與型號料理指南>' \
  --interface 'short_description=<25～64 字元的簡短說明>' \
  --interface 'default_prompt=使用 $cooking-with-<model>，告訴我如何操作這台設備。'
```

如果目前的 agent 能直接使用 `skill-creator`，也可以讓 agent 執行初始化；輸出位置仍指定為 `@.agents/skills`。初始化後刪除未使用的範例或空白資源，只保留真正需要的內容。

## 撰寫 references

每個參考檔開頭先寫來源與適用範圍，例如：

```markdown
# 操作與安全

本文整理自「<手冊名稱>」<版本> 第 <頁碼> 頁，只保留適用 <目標型號> 的內容。
```

整理時遵守以下規則：

1. 保留原廠名稱、數值、單位、條件與例外。
2. 把操作順序改寫成短步驟，但不要改變警告強度。
3. 表格每列只表達一個清楚條件，避免把不同型號或模式合併。
4. 手冊沒寫的時間、水量、份量或完成判定，明確標示「手冊未提供」。
5. 不用一般料理經驗填補成原廠值。
6. 相對連結只指向同一個 Skill 內的檔案；Skill 不應依賴 repository 外部的私人筆記。
7. 不把 CulinaForge 產生的完整食譜存進 `references/`。

若 Skill 需要官方食譜或實測資料，必須在檔案開頭標示資料性質並定義來源優先序。實測只提升真正做過的條件與步驟；其他推算欄位仍保持較低順位。

## 撰寫 SKILL.md

### Frontmatter

Frontmatter 只放 `name` 與 `description`：

```yaml
---
name: cooking-with-<model>
description: 依據 <設備商> <完整型號> 使用手冊提供操作、料理設定與安全限制，包含 <主要功能>。用於單獨查詢 <型號>，或在 CulinaForge 食材提示中與其他設備 Skill 協作；本 Skill 只包含 <型號>，不提供其他烹調設備知識。
---
```

`description` 是 Skill 的主要觸發依據，要同時寫出「能做什麼」與「什麼情況要使用」。不要把觸發條件只放在正文，否則 agent 在尚未載入正文時看不到。

### 正文

正文用指令式語氣，建議包含：

1. **核心原則**：先提供可操作流程，再補限制或替代方案。
2. **來源與設備邊界**：只採本 Skill 的內嵌來源，只回答目標型號。
3. **查找資料**：不同問題應讀哪個 reference，以及何時需要完整讀取。
4. **判斷輸入**：食材狀態、重量、厚度、帶骨、容器、配件和目標口感。
5. **模式與硬邊界**：設備可用設定、不能混用的配件、時間上限和自動行為。
6. **多設備協作**：只負責本設備；其他設備由對應 Skill 提供設定。
7. **回答格式**：準備、配件、操作、完成判定、補救與來源說明。
8. **故障與清潔**：先處理立即風險，不教使用者拆機。
9. **最後檢查**：逐項確認數值、型號、來源、安全與輸出位置。

保持 `SKILL.md` 精簡，通常不超過 500 行。若正文開始大量列舉模式、錯誤碼、尺寸或食譜，把細節移到直接連結的 `references/`。

## 建立 agents/openai.yaml

`agents/openai.yaml` 是 OpenAI 產品使用的選用介面資訊，不屬於跨平台 Agent Skills 的核心規格。至少設定三個欄位：

```yaml
interface:
  display_name: "<品牌與型號料理指南>"
  short_description: "<25～64 字元的簡短說明>"
  default_prompt: "使用 $cooking-with-<model>，告訴我如何操作這台設備。"
```

所有字串都加引號。`default_prompt` 要明確包含 `$skill-name`。沒有現成圖示、品牌色或工具依賴時不要自行發明欄位。

初始化後若 `SKILL.md` 用途有變，使用 `generate_openai_yaml.py` 重新產生，不要讓介面說明和 Skill 實際能力不同：

```bash
python3 <skill-creator 根目錄>/scripts/generate_openai_yaml.py \
  .agents/skills/cooking-with-<model> \
  --interface 'display_name=<品牌與型號料理指南>' \
  --interface 'short_description=<25～64 字元的簡短說明>' \
  --interface 'default_prompt=使用 $cooking-with-<model>，告訴我如何操作這台設備。'
```

## 接上 CulinaForge 食譜流程

設備 Skill 必須能參與 CulinaForge 的食材提示，但仍只提供自己的設備知識。正文應要求 agent：

1. 解析用餐人數、餐點名稱、食材清單與特別說明。
2. 食材缺少重量時，依一般包裝、顆數或份量建立合理估算並明列假設。
3. 只為目標設備提供模式、功率或火力、配件、位置、安全限制與檢查點。
4. 讓其他設備 Skill 各自提供自己的設定，由主 agent 合併時間線。
5. 把手冊未提供的切法、調味、水量或時間標為「食譜設計／合理估算」，不得寫成原廠建議。
6. 不為了使用設備而增加沒有必要的步驟。
7. 把完成的 Markdown、PDF、PNG 食譜放到 `output/recipe-card/`，暫存檔只放 `tmp/`；不把完整食譜存回設備 Skill。

食材提示格式與多設備食譜輸出規則請參考[入門使用者指南](basic-user-guide.md)和[進階使用者指南](advanced-user-guide.md)。

## 驗證與前向測試

### 1. 執行結構驗證

使用 `skill-creator` 的驗證工具：

```bash
python3 <skill-creator 根目錄>/scripts/quick_validate.py \
  .agents/skills/cooking-with-<model>
```

修正所有 frontmatter、名稱與目錄錯誤，直到驗證通過。

### 2. 檢查內容一致性

至少確認：

- 目錄名稱和 frontmatter `name` 相同。
- `description`、正文與 `agents/openai.yaml` 描述同一套能力。
- 所有 Markdown 相對連結都能解析。
- `references/` 沒有其他設備或其他型號的專屬內容。
- 所有模式、功率、火力、溫度、分鐘、尺寸、配件與錯誤碼都能回到來源頁面。
- 安全警告沒有被縮短成較弱的建議。
- 手冊未提供的料理資訊沒有被標成原廠值。

可搜尋其他型號、其他設備名稱與常見跨設備詞彙，確認沒有誤混入 Skill。例如目標是雙口 IH 爐時，搜尋共用手冊中的單口型號；目標是蒸烘烤箱時，搜尋 `IH`、`瓦斯` 或其他設備型號。

### 3. 做實際提示測試

至少測四種情境：

1. **基本操作**：詢問一個模式、按鍵或配件的操作步驟。
2. **安全與故障**：詢問危險狀況或錯誤碼，確認先給立即處置。
3. **單設備食譜**：提供食材、份量與偏好，確認能區分手冊設定與食譜估算。
4. **多設備食譜**：和另一個 Skill 一起使用，確認只輸出本設備負責的步驟。

對複雜 Skill 做前向測試時，讓新的 agent 只看到 Skill 路徑與一般使用者提示，不提供預期答案或修改理由。這樣才能驗證 Skill 本身是否足以引導正確行為。

## 可直接使用的建立提示

把下列範本交給能讀寫 CulinaForge repository 的 agent：

```text
請使用 $skill-creator，依設備商提供的使用手冊建立一個 CulinaForge 烹調設備 Agent Skill。

設備品牌與完整型號：【請填寫】
使用手冊：【附件或檔案路徑】
手冊版本／日期：【請填寫；未知就寫未知】
Skill 名稱：【cooking-with-<model>】
輸出目錄：@.agents/skills

請依下列規則執行：
1. 一個 Skill 只包含這一台設備。若手冊同時涵蓋其他型號，只保留目標型號欄位與確定適用的共通規則；不要保留其他型號的規格、操作、安裝尺寸或故障處置。
2. 先列出來源文件、版本、頁碼範圍與使用限制，再擷取文字並回看原頁面核對數字、表格、圖示、腳註與安全警告。
3. 使用 skill-creator 的 init_skill.py 初始化 `@.agents/skills/<skill-name>`，只建立需要的 references；不要建立 Skill 內 README、安裝指南或變更記錄。
4. 將詳細的模式、功率或火力、溫度、時間上限、配件、容器、層位、鍋具、安全、清潔、錯誤碼、規格與安裝資料整理到 references/。每個檔案標明來源版本與頁碼。
5. SKILL.md frontmatter 只放 name 與 description。description 同時寫出 Skill 能力與觸發情境；正文使用指令式語氣，包含來源邊界、查找流程、料理判斷、多設備協作、回答格式與最後檢查。
6. 手冊沒提供的食譜時間、水量、切法、份量或完成判定，清楚標示為「手冊未提供」；在 CulinaForge 產生食譜時可以另作「食譜設計／合理估算」，但不得歸因於手冊。
7. 更新 agents/openai.yaml 的 display_name、25～64 字元 short_description 與明確包含 `$<skill-name>` 的 default_prompt；沒有提供圖示或品牌色時不要自行新增。
8. 完整跨設備食譜不要存進 Skill。完成食譜放 @output/recipe-card；所有 OCR、渲染與測試暫存檔放 @tmp。
9. 執行 quick_validate.py，檢查相對連結，搜尋並移除其他設備／型號專屬內容，再用基本操作、安全故障、單設備食譜與多設備食譜做前向測試。

完成後回報：
- 新 Skill 路徑與目錄結構。
- 採用的來源、版本與頁碼範圍。
- 手冊未提供或仍有歧義的欄位。
- 驗證與前向測試結果。
- 實際新增或修改的檔案。
```

## 完成檢查表

- [ ] Skill 名稱符合 `cooking-with-<model>`，目錄與 frontmatter `name` 相同。
- [ ] 一個 Skill 只包含一台設備，沒有其他型號的專屬內容。
- [ ] 原始手冊與 OCR 暫存檔只放在允許的位置，且遵守設備商使用條款。
- [ ] 每個 reference 都標明來源、版本、頁碼與適用型號。
- [ ] `SKILL.md` 精簡、使用指令式語氣，詳細數字放在 `references/`。
- [ ] Frontmatter 只有 `name` 與 `description`，description 能正確觸發 Skill。
- [ ] `agents/openai.yaml` 的三個介面欄位與 Skill 能力一致。
- [ ] 手冊資料與食譜設計估算清楚分開。
- [ ] 完整食譜只輸出到 `output/recipe-card/`，暫存檔只放 `tmp/`。
- [ ] `quick_validate.py`、相對連結與跨型號搜尋全部通過。
- [ ] 基本操作、安全故障、單設備與多設備四種測試均符合預期。

可以參考專案內現有的 [Panasonic NN-BS1700 Skill](../.agents/skills/cooking-with-nn-bs1700/SKILL.md) 與 [Rinnai RB-2232H Skill](../.agents/skills/cooking-with-rb-2232h/SKILL.md)，觀察不同設備如何拆分來源與維持單一型號邊界。
