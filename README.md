# CulinaForge

CulinaForge 是一個以烹調設備為核心的 Agent Skills 與專屬食譜專案，提供兩項功能：

1. 依一份或多份指定烹調設備使用手冊建立 Agent Skills；每個 Skill 只包含一台設備的知識、操作方式與安全限制。
2. 依使用者指定的一個或多個設備 Skill、食材內容、用餐人數、餐點名稱與特別說明，整合成可直接操作的專屬食譜。

本專案是非官方整理，不代表任何設備製造商的意見、授權或保證。設備操作與安全事項應以機身標示、包裝指示及最新版官方使用手冊為準。

## 現有烹調設備 Skills

| Skill | 烹調設備 | 範圍 |
| --- | --- | --- |
| [`cooking-with-nn-bs1700`](.agents/skills/cooking-with-nn-bs1700/SKILL.md) | Panasonic NN-BS1700 微波蒸烘烤箱 | 微波、蒸氣、烘烤、燒烤、自動行程、配件、層位與完成判定 |
| [`cooking-with-rb-2232h`](.agents/skills/cooking-with-rb-2232h/SKILL.md) | Rinnai RB-2232H IH 智慧感應爐 | 1～9 段火力、Booster、定時器、雙爐限制、鍋具與安全操作 |

新增設備時，為每台設備建立獨立的 `cooking-with-<model>` Skill 目錄。不得把另一台設備的說明書、設定或食譜知識複製進同一個 Skill；多設備分工只在產生食譜時由 agent 整合。完整製作流程請見[從烹調設備使用手冊建立專屬 Agent Skill](document/create-agent-skill-for-cooking-appliance.md)。

## 食譜提示範本

```text
我有下列食材，推薦我使用 $<agent skill name 1> 與 $<agent skill name 2> 做一道 <實用人數> 人份的 <餐點名稱>：

1. <食材名稱 1> <份數> 份，每份 <重量> g/kg
2. <食材名稱 2> <份數> 份，每份 <重量> g/kg
3. <列出所有食材，格式如上 1>

特別說明：
<說明偏好或者指定的食材料理方式>
```

可以指定一個或多個設備 Skill。食材沒有提供重量時，agent 會依一般份量作合理估算、明列假設，並在設備設定與食品安全需要時採較保守的首輪起點。

完整範例與跨平台寫法請閱讀[入門使用者指南](document/basic-user-guide.md)；規劃多設備分工與產出檔案的完整規則請閱讀[進階使用者指南](document/advanced-user-guide.md)。要依設備商使用手冊新增 Skill，請改讀[烹調設備 Agent Skill 製作指南](document/create-agent-skill-for-cooking-appliance.md)。

## 食譜輸出

產生的食譜 Markdown、PDF 與 PNG 一律放在 `output/recipe-card/`。產生過程需要的原始圖、渲染結果、轉檔資料或其他暫存檔，一律放在 `tmp/`；`tmp/` 已由 [`.gitignore`](.gitignore) 排除，不會提交到 GitHub。

同名檔案不得覆蓋：

- 第一次產生時使用 `<餐點名稱>.md`、`<餐點名稱>.pdf` 與 `<餐點名稱>.png`。
- 任一同名目標已存在時，三種格式共同使用同一個產生時間，命名為 `<餐點名稱>-yyyymmdd-hhmmss-.<副檔名>`。
- 例如 `番茄海鮮速拌細麵-收乾版-20260804-180559-.png` 代表 2026 年 8 月 4 日 18:05:59 產生。

## 使用文件

| 主題或 Agent | 文件 |
| --- | --- |
| 從設備商使用手冊建立 Skill | [烹調設備 Agent Skill 製作指南](document/create-agent-skill-for-cooking-appliance.md) |
| Codex | [Codex 使用指南](document/CODEX.md) |
| Claude Code／Claude Cowork | [Claude 使用指南](document/CLAUDE.md) |
| Gemini CLI／Google AI agent | [Gemini 使用指南](document/GEMINI.md) |
| GitHub Copilot | [Copilot 使用指南](document/COPILOT.md) |

## 來源與安全邊界

每個設備 Skill 的 `references/` 是該設備的來源邊界。設備模式、功率、火力、配件、層位、時間上限、錯誤碼與安全限制必須由相應 Skill 核對；手冊沒有提供的食譜時間、份量或食材處理，可由 agent 作食譜設計，但必須清楚標示為估算或首輪起點，不得寫成原廠建議。

處理肉類、蛋、魚、海鮮與剩食時，應以中心溫度或明確完成狀態收尾。設備異常、爐面或門體損壞、持續冒煙、火花、焦味或錯誤碼無法排除時，立即停止使用並依官方手冊聯絡服務人員。

## 版權、商標與免責聲明

Panasonic、Rinnai、國際牌、林內及其他品牌、產品名稱、標誌與商標均屬其各自權利人所有。官方使用手冊、食譜、圖片、圖表及其他官方資料不適用本專案的 CC0 授權；請從權利人提供的官方管道取得文件並遵守其使用條款。

除官方文件、商標、圖片、引用內容及其他第三方素材外，本專案自行撰寫與整理的原創內容依 [CC0 1.0 Universal](LICENSE) 提供。內容按現況提供，可能包含錯誤、遺漏、合理估算或尚未驗證的首輪設定；使用者應自行確認設備與食品安全並承擔使用風險。
