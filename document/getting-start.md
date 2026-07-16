# 開始使用 NN-BS1700 料理 skill

這份指南提供四組可直接修改的 prompt：先產生料理流程，再把流程存成食譜、加入 skill，實際煮過後再升級為實測食譜。這樣下次詢問相同或相近食材時，agent 可以先找到你的記錄，不必每次從通用資料重新推算。

料理判斷的核心是 `cooking-with-nn-bs1700`；範本也會叫用 `write-like-human` 來整理文字，並在修改 skill 時叫用 skill creator。`write-like-human` 與 skill creator 都是輔助工具，沒有安裝時仍可執行本指南，但要保留 prompt 內的來源標示、檔案範圍與驗證要求。

第二至第四步會修改專案檔案，請先讓 agent 在本 repository 根目錄工作，並確認它有檔案寫入權限。建議在同一個對話依序執行；若換到新對話，請把前一步的完整結果一併貼上。若平台不支援以 `@` 指向檔案，直接使用 `@` 後面的相對路徑即可。

範本中的 `【請填寫】` 都要換成自己的資料。食材的生鮮／冷藏／冷凍狀態、重量、數量和最厚處厚度，會直接影響設定；肉、魚、蛋與剩食也應以中心溫度或明確完成狀態收尾。

## 先確認平台的 skill 名稱與叫用方式

本頁 prompt 範本的第一行採用 Codex 語法。`$skill-name` 是 Codex 用來明確選取 skill 的寫法，不是 Agent Skills 共通規格；Claude、Gemini 與 GitHub Copilot 不應直接照抄 `$`。如果不確定目前介面支援哪種符號，最穩妥的跨平台寫法是直接用自然語言，例如：「請使用 cooking-with-nn-bs1700 skill」。

本頁寫的 `$skill-creator` 也特別指 Codex 提供的 `skill-creator` system skill。其他 agent 可能使用同名但內容不同的 skill，也可能使用完全不同的名稱。以下是依 2026-07-16 官方文件整理的對照：

| Agent | 建立或更新 skill 的輔助工具 | 明確叫用 skill 的方式 |
| --- | --- | --- |
| Codex | Codex system skill 名稱是 `skill-creator`。 | 使用 `$skill-name`，例如 `$cooking-with-nn-bs1700`、`$skill-creator`。 |
| Claude Code | Anthropic 官方提供的是 `skill-creator` plugin，但它不是 Codex 的 system skill。先執行 `/plugin install skill-creator@claude-plugins-official`，再執行 `/reload-plugins`。 | Claude Code 使用 `/skill-name`，例如 `/cooking-with-nn-bs1700`；安裝 creator plugin 後可使用 `/skill-creator`，也可以在自然語言中寫出 skill 名稱。 |
| Gemini CLI | 內建 creator 名稱是 `skill-creator`，不需要另行安裝。 | 沒有讓使用者手動叫用單一 skill 的前置符號。直接寫「請使用 cooking-with-nn-bs1700」或描述任務，由 Gemini 呼叫 `activate_skill`；`/skills` 只用於列出、啟用、停用與重新載入 skill。 |
| GitHub Copilot | 依目前 GitHub 官方說明，沒有指定一個內建、通用且固定名稱的 skill creator。GitHub 組織下、標示為社群貢獻的 Awesome Copilot 集合提供可另行安裝的通用 `make-skill-template`；Microsoft 官方另有名為 `skill-creator` 的 skill，但內容偏向 Azure SDK 與 Microsoft Foundry，不適合直接當成本食譜 skill 的通用 creator。 | Copilot CLI 使用 `/skill-name`，例如 `/cooking-with-nn-bs1700`；若已安裝 Awesome Copilot 的 creator，可用 `/make-skill-template`。其他 Copilot 介面若沒有 slash skill 選單，直接在自然語言中寫出 skill 名稱。 |

官方依據：

- [Claude Code：Skills 與 `/skill-name` 叫用方式](https://code.claude.com/docs/en/skills)，以及同頁的官方 `skill-creator` plugin 安裝說明。
- [Gemini CLI：內建 `skill-creator`](https://geminicli.com/docs/cli/creating-skills/)與 [`activate_skill` 只能由 Gemini agent 呼叫](https://geminicli.com/docs/tools/activate-skill/)。
- [GitHub Copilot CLI：使用 `/SKILL-NAME`](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-skills)，以及 GitHub 組織下 Awesome Copilot 集合的 [`make-skill-template`](https://github.com/github/awesome-copilot/tree/main/skills/make-skill-template)。
- [Microsoft `skill-creator`](https://github.com/microsoft/skills/blob/main/.github/skills/skill-creator/SKILL.md)的說明明列其重點是 Azure SDK 與 Microsoft Foundry。

因此，下面程式碼區塊中的 `$cooking-with-nn-bs1700`、`$skill-creator` 與 `$write-like-human` 都是 Codex 版本。使用其他 agent 時，只替換第一行的 skill 名稱與叫用方式，其餘料理條件、檔案路徑和修改限制維持不變：

- Claude Code：把 `$cooking-with-nn-bs1700` 改成 `/cooking-with-nn-bs1700`；需要修改 skill 時，使用已安裝的 `/skill-creator`。Claude.ai 或 Cowork 不一定支援 Claude Code plugin 與 slash skill，請改用自然語言並確認所需 skill 已上傳或啟用。
- Gemini CLI：移除所有 `$`，改寫成「請使用 cooking-with-nn-bs1700 與內建 skill-creator」。Gemini 會在取得同意後自行啟用；不要把 `/skills` 當成單一 skill 的叫用前綴。
- GitHub Copilot CLI：把 `$cooking-with-nn-bs1700` 改成 `/cooking-with-nn-bs1700`；若已另外安裝 `make-skill-template`，修改 skill 時再指定 `/make-skill-template`。沒有安裝 creator 時，保留 prompt 內的完整修改規則，讓 Copilot 直接依規則編輯。
- `write-like-human` 不是上述平台保證內建的 skill。未安裝時刪除這個名稱即可，不要自行猜測替代名稱。

## 1. 產生料理步驟

一次可以指定一種或多種模式，例如只要純蒸，或比較微波、蒸、烘烤與燒烤。不是每種食材都適合四種模式；請讓 agent 說明不適合的原因，不要硬湊出危險或機器無法執行的設定。

### Prompt 範本

```text
請使用 $cooking-with-nn-bs1700 與 $write-like-human，為 Panasonic NN-BS1700 產生一套可以直接照著操作的料理流程。

料理／食材：【料理名稱與主食材】
起始狀態：【生鮮／冷藏 0～5°C／冷凍約 -18°C】
份量：【重量、數量】
最厚處：【厚度；肉魚另寫帶骨、填餡或厚薄是否均一】
目標口感：【濕潤、焦香、酥脆、全熟、回溫等】
想使用的模式：【微波／蒸／烘烤／燒烤，可複選】
現有配件與容器：【原廠雙面烤盤、方盤、耐熱玻璃容器等】
其他限制：【調味、時間、不能使用的配件；沒有則寫無】

請先依 cooking-with-nn-bs1700 的來源優先序查找完全相符的食譜，再找同類、相近厚度與相同成品目標的資料。若只能類比，明確標示哪些設定是「整理食譜／類比推算」，不要寫成原廠值或實測最佳值，也不要把衝突資料平均。

先給你最推薦的一套流程，再列我指定的其他模式。每套流程都要包含：
1. 適用條件與採用的假設。
2. 解凍、擦乾、劃口、加水或抹油等準備。
3. 容器或配件、盤面、層位，以及是否預熱。
4. 每一段的面板模式、功率或溫度、時間，和翻面、攪拌、換位或加入蒸氣的時機。
5. 中心溫度或可觀察的完成判定，以及靜置時間。
6. 中心未達標、表面不上色或成品太乾時的短段補救。
7. 只有完全相符且已核實時才列自動行程；否則說明為什麼手動較穩定。

若某個指定模式不適合這項食材、容器或目標，直接說明限制並提供安全替代流程。使用自然、精簡的繁體中文。這一步只回答料理方法，不修改任何檔案。
```

### 範例：比較雞腿排的四種做法

```text
請使用 $cooking-with-nn-bs1700 與 $write-like-human，為 Panasonic NN-BS1700 產生一套可以直接照著操作的料理流程。

料理／食材：冷藏去骨雞腿排
起始狀態：冷藏 0～5°C
份量：2 片，總重約 400 g
最厚處：約 2 cm，無填餡，厚薄大致均一
目標口感：中心全熟但保有肉汁，表皮有焦香
想使用的模式：請比較純微波、純蒸、烘烤、燒烤，最後再推薦一套可組合模式的首選流程
現有配件與容器：原廠雙面烤盤、方盤、2 L 耐熱玻璃容器
其他限制：不使用鋁箔，醃料含少量醬油與糖

請先依 cooking-with-nn-bs1700 的來源優先序查找完全相符的食譜，再找同類、相近厚度與相同成品目標的資料。若只能類比，明確標示哪些設定是「整理食譜／類比推算」，不要寫成原廠值或實測最佳值，也不要把衝突資料平均。

先給最推薦的流程，再比較其他模式。每一種都要寫出前處理、配件、盤面、層位、預熱、面板模式、功率或溫度、首輪時間、翻面或加蒸氣時機、雞肉完成溫度、靜置與補時方式。若純微波或其他單一模式無法達到表皮焦香，請直接說明，不要硬湊設定。這一步不要修改檔案。
```

## 2. 把產生的流程存成新食譜

確認第一步的流程符合你的需求後，再執行這個 prompt。這一步只新增一個食譜檔，不更新索引或來源裁決表，方便先單獨檢查內容。

目前的編號分類如下：

- `10xx`：魚與肉。
- `11xx`：蔬菜。
- `12xx`：麵食與餃點。
- `13xx`：蛋料理。
- `14xx`：熟食回溫。

編號只代表分類，不代表來源優先序。agent 應先列出現有檔案，再依所屬分類使用執行當下的下一個未使用編號；不可覆蓋、重編或移動舊食譜。

### Prompt 範本

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，把下列料理流程整理成 cooking-with-nn-bs1700 的一個新食譜檔。

料理名稱：【料理名稱】
第一步產生的完整流程：
【貼上完整流程；若仍在同一對話，也可寫「使用上一則回覆的完整流程」】

skill 根目錄：@.agents/skills/cooking-with-nn-bs1700
食譜目錄：@.agents/skills/cooking-with-nn-bs1700/references/recipe

動手前請：
1. 完整讀取 skill 根目錄的 SKILL.md 與 references/source-priority.md。
2. 列出 references/recipe 現有檔案，並搜尋料理名、主食材、同義詞、狀態與目標，避免建立重複食譜。
3. 依現行分類選擇編號：10xx 魚肉、11xx 蔬菜、12xx 麵食與餃點、13xx 蛋、14xx 熟食回溫。使用該分類下一個未使用編號，不覆蓋或重編既有檔案。
4. 檔名使用「<四位數編號>-<清楚且可搜尋的料理名>.md」。

這份流程尚未實際烹煮驗證。新檔開頭的資料性質必須明確寫成「整理食譜／類比推算」，並指出哪些值來自既有來源、哪些是首輪試做起點；不可稱為實測食譜、原廠食譜或最佳值。

食譜內容至少包含適用條件與限制、材料或份量、準備、容器／配件與位置、分段操作、完成判定與補時，以及已核實的自動行程。只保留 NN-BS1700 面板可執行的設定，遵守金屬配件、微波、蒸氣、解凍與食品安全硬邊界。來源沒有寫重量、厚度或時間時，明說未註明或屬類比推算，不自行補成確定事實。

依 skill-creator 的精簡原則，詳細資料只放在這個 recipe 檔。這一步只新增一個食譜檔，不修改 SKILL.md、source-priority.md、recipes-by-category.md 或其他檔案。

完成後請回報：
- 新檔的相對路徑。
- 分類與編號的判斷理由。
- 檔內哪些欄位是來源整理、哪些是類比推算。
- 實際修改的檔案清單。
```

### 範例：把雞腿排流程存入魚肉類

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，把上一則回覆的「冷藏去骨雞腿排」完整流程整理成 cooking-with-nn-bs1700 的一個新食譜檔。

skill 根目錄：@.agents/skills/cooking-with-nn-bs1700
食譜目錄：@.agents/skills/cooking-with-nn-bs1700/references/recipe

請先完整讀取 SKILL.md 與 references/source-priority.md，列出 recipe 目錄並搜尋「雞腿排、去骨雞腿、雞肉、冷藏、焦香」。這是魚肉類，應使用 10xx；請依執行當下的現有檔案選下一個未使用編號。若下一號剛好是 1007，可使用 `1007-烤冷藏去骨雞腿排.md`；若 1007 已存在，就順延，不要覆蓋。

這份食譜還沒實際煮過，檔案開頭必須標為「整理食譜／類比推算」。請區分來源支持的安全或操作規則，與尚待試做的模式、溫度和時間。完整寫出適用條件、兩片約 400 g、最厚約 2 cm、準備、配件與層位、推薦組合流程、其他模式、中心溫度、補時及限制。

這一步只新增食譜檔，不修改 SKILL.md、source-priority.md 或 recipes-by-category.md。完成後回報新檔路徑、選號理由、推算欄位與修改檔案清單。
```

## 3. 把未驗證食譜加入 skill

檢查新食譜檔後，再用這個 prompt 更新 skill 的查找與來源裁決內容。最重要的是在 `references/source-priority.md` 的「食譜來源裁決總表」加入該食譜，且 `資料性質` 必須是 `整理食譜／類比推算`。

`references/recipes-by-category.md` 是來源 102 的固定整理表，不是新增食譜索引，因此不要把新食譜加進去。

### Prompt 範本

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，依下列新食譜更新 cooking-with-nn-bs1700 skill。

新食譜：【@.agents/skills/cooking-with-nn-bs1700/references/recipe/<編號>-<料理名>.md】
skill 根目錄：@.agents/skills/cooking-with-nn-bs1700

請完整讀取 SKILL.md、references/source-priority.md 與新食譜，並檢查 recipe 目錄目前的檔名。只做讓這份食譜可被正確找到、採用與裁決所需的最小修改：

1. 確認新食譜檔開頭仍標示「整理食譜／類比推算」，沒有把推算值寫成實測、原廠或最佳值。
2. 在 references/source-priority.md 的「食譜來源裁決總表」新增或修正這份食譜的唯一一列。連結要指向正確 recipe 檔；`資料性質` 欄的值必須精確寫成 `整理食譜／類比推算`。
3. 「採用設定／可用範圍」只摘要可搜尋、可判斷是否適用的條件；「來源裁決與必須揭露的限制」要列出類比推算欄位與不可直接套用的情況。
4. 只有這份食譜帶來新的查找規則、特殊安全分支或容易漏掉的具名情境時，才精簡更新 SKILL.md。不要把整份食譜複製到 SKILL.md。
5. 不要把新食譜加入 references/recipes-by-category.md；它是 102 的固定整理表。也不要改寫既有食譜或處理無關問題；若發現無關的不一致，只需在結果中指出。
6. 確認 recipe 檔、source-priority.md 與必要的 SKILL.md 敘述彼此一致，Markdown 相對連結可解析。使用 skill-creator 提供的驗證方式檢查 skill；若驗證工具不可用，說明你改用哪些檢查。

完成後列出修改檔案、裁決表新增的摘要、是否修改 SKILL.md 及原因，以及驗證結果。不要建立額外說明檔，也不要提交 git commit。
```

### 範例：加入尚未試做的雞腿排食譜

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，依新食譜 @.agents/skills/cooking-with-nn-bs1700/references/recipe/1007-烤冷藏去骨雞腿排.md 更新 skill。若上一步實際產生的編號或檔名不同，請改用那個真實路徑，不要另建 1007。

請完整讀取 SKILL.md、references/source-priority.md 與這份新食譜。在「食譜來源裁決總表」加入唯一一列，連結到正確檔案，`資料性質` 精確填 `整理食譜／類比推算`。摘要適用條件為冷藏去骨雞腿排、兩片約 400 g、最厚約 2 cm、目標是全熟而表皮焦香；把尚未試做的模式、時間與溫度清楚列在限制欄，不能升成實測順位。

只有未來查詢「雞腿排、去骨雞腿、冷藏雞肉、焦香雞皮」會因現有 SKILL.md 規則漏掉這份檔案時，才補一小段查找提示。不要複製完整流程，也不要更新 recipes-by-category.md。最後檢查相對連結、資料性質與來源順位一致，並回報修改檔案和驗證結果。
```

## 4. 實際煮過後升級為實測食譜

完成料理後，記下實際條件與機器設定。即使只記得一部分，也請如實填寫；agent 應把缺少的欄位標成未記錄，不能自行補齊。若你調整過原流程，請同時寫下最初結果與最後成功的補時，這些資料比一句「成功」更能幫助下一次料理。

同一檔內若有四種做法，但你只試過其中一種，整列的 `資料性質` 可以更新為 `實測食譜`，不過檔案必須逐項說清楚：只有實際使用的條件與步驟取得實測順位，其他替代路線仍是整理或類比推算。

### Prompt 範本

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，依我的實際烹煮記錄更新 cooking-with-nn-bs1700 skill。

食譜檔：【@.agents/skills/cooking-with-nn-bs1700/references/recipe/<編號>-<料理名>.md】
實測日期：【YYYY-MM-DD；不想記錄可寫未提供】
實際食材與狀態：【料理、品牌或部位、生鮮／冷藏／冷凍】
實際份量：【重量、數量】
實際最厚處：【厚度、帶骨／填餡、厚薄是否均一】
實際配件與位置：【容器、盤面、層位】
實際操作：【依序列出模式、功率／溫度、時間、預熱、翻面、攪拌、加蒸氣與補時】
完成結果：【中心溫度；沒有溫度計則寫切面、冷點、質地與顏色】
靜置時間：【分鐘】
與原食譜不同之處：【沒有則寫完全照原流程】
下次想調整之處：【沒有則寫無】

請先完整讀取 SKILL.md、references/source-priority.md 與這份食譜，再依實際記錄更新：

1. 把食譜檔開頭更新為「實測食譜」，明列哪些份量、狀態、厚度、配件、模式與時間已實際驗證。未提供的資料寫未記錄，不可推測。
2. 只有實際做過的步驟與欄位取得實測順位。未試過的替代模式、來源補充與類比值要保留原本標示，不能因同檔有一條成功路線就全部升級。
3. 若原流程需要補時或調整才成功，把最後可行的完整順序寫清楚；失敗的首輪設定可保留為限制或修正記錄，不要仍當成主要實測設定。
4. 更新 references/source-priority.md「食譜來源裁決總表」中的同一列，不新增第二列。`資料性質` 欄的值必須精確改成 `實測食譜`，並同步更新實測範圍與仍須揭露的限制。
5. 只有查找規則或安全分支因此改變時，才精簡更新 SKILL.md。不要更新 recipes-by-category.md，不要把整份食譜複製到 SKILL.md，也不要修改無關食譜。
6. 實測結果若碰到 NN-BS1700 的模式／配件硬邊界或食品安全疑慮，不要把危險操作寫成建議；保留事實記錄、指出衝突，並採安全限制作為未來操作邊界。
7. 檢查食譜檔、裁決表與 SKILL.md 必要敘述是否一致，確認相對連結可解析，並使用 skill-creator 的驗證方式檢查 skill。

完成後回報哪些欄位升級為實測、哪些仍是整理或類比推算、修改檔案清單與驗證結果。不要提交 git commit。
```

### 範例：把成功的雞腿排流程升級為實測

以下數字只示範 prompt 要記錄到什麼程度，不代表已驗證的 NN-BS1700 設定。請換成你自己的實際資料，不要原樣寫回 skill。

```text
請使用 $cooking-with-nn-bs1700、$skill-creator 與 $write-like-human，依我的實際烹煮記錄更新 cooking-with-nn-bs1700 skill。

食譜檔：@.agents/skills/cooking-with-nn-bs1700/references/recipe/1007-烤冷藏去骨雞腿排.md
實測日期：2026-07-20
實際食材與狀態：冷藏去骨雞腿排，冷藏約 4°C
實際份量：2 片，總重 405 g
實際最厚處：一片約 1.8 cm，另一片約 2 cm；無骨、無填餡
實際配件與位置：完全照食譜檔的推薦組合流程
實際操作：完全照食譜檔的推薦組合流程，沒有額外補時；其他三種單一模式沒有試
完成結果：兩片最厚處分別為 76°C 與 78°C；切面全熟、沒有冷點，雞皮有上色，肉汁仍可接受
靜置時間：3 分鐘
與原食譜不同之處：沒有
下次想調整之處：想讓雞皮再脆一點，但這次沒有追加燒烤，因此不能把額外燒烤時間記成實測

請完整讀取 SKILL.md、references/source-priority.md 與食譜檔。把檔案標為「實測食譜」，但只有這次實際使用的推薦組合流程、份量、厚度、配件與結果取得實測順位；未試過的純微波、純蒸、單獨烘烤或燒烤路線仍保留「整理／類比推算」。

在「食譜來源裁決總表」更新原本那一列，不要新增重複列，並把 `資料性質` 精確改成 `實測食譜`。摘要本次實測範圍，也保留尚未測試其他模式與不同重量、厚度時不可直接套用的限制。不要更新 recipes-by-category.md。最後回報實測與未實測欄位、修改檔案及驗證結果。
```

## 完成後檢查

每次讓 agent 修改食譜或 skill 後，先看它列出的檔案，再檢查版本差異。至少確認：

- 新食譜放在 `references/recipe/`，分類和編號沒有撞號。
- 食譜檔開頭與「食譜來源裁決總表」的 `資料性質` 一致。
- 未實測時是 `整理食譜／類比推算`；實際完成料理後才改成 `實測食譜`。
- 實測只涵蓋真正做過的條件與步驟，沒有順便升級未試過的替代方案。
- `recipes-by-category.md` 沒有因新增個人食譜而被改寫。
- 配件、層位、微波金屬限制、解凍方式與完成判定都符合 NN-BS1700 的硬邊界。

食譜記錄越具體，後續建議越能貼近你的份量、食材與口感。不確定的地方留白或標成未記錄，比補上一個看似完整但沒有做過的數字更有用。
