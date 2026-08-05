# 在 Codex 安裝與使用 CulinaForge Skills

本指南適用於 ChatGPT 桌面版中的 Codex、Codex CLI 與 Codex IDE extension。CulinaForge 的烹調設備 Skills 位於 `.agents/skills/`；每個 Skill 只包含一台設備，產生食譜時可以同時叫用多個 Skill。

## 平台設定檔

每個 Skill 的 `agents/openai.yaml` 提供 Codex 顯示名稱、簡短說明與預設提示。它是 OpenAI 產品的選用介面資訊；跨平台核心仍是 `SKILL.md` 的 `name`、`description` 與內文。

## 安裝

先取得 repository：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

### 專案安裝

從 repository 根目錄啟動 Codex，即可使用 `.agents/skills/` 內所有設備 Skills，不必複製檔案。

### 個人安裝

若只想在其他專案使用指定設備，將該設備的完整 Skill 目錄複製到個人目錄。`SKILL.md`、`agents/` 與 `references/` 必須保持在一起。

macOS／Linux：

```bash
mkdir -p "$HOME/.agents/skills"
cp -R .agents/skills/cooking-with-nn-bs1700 "$HOME/.agents/skills/"
cp -R .agents/skills/cooking-with-rb-2232h "$HOME/.agents/skills/"
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
Copy-Item -Recurse .agents/skills/cooking-with-nn-bs1700 "$HOME/.agents/skills/"
Copy-Item -Recurse .agents/skills/cooking-with-rb-2232h "$HOME/.agents/skills/"
```

請擇一安裝範圍，避免同名的專案版與個人版同時出現。

## 確認與使用

執行 `/skills` 或輸入 `$` 搜尋已安裝的 Skill。產生多設備食譜時，在提示中同時寫出所有 Skill：

```text
我有下列食材，推薦我使用 $cooking-with-rb-2232h 與 $cooking-with-nn-bs1700 做一道三人份的咖哩飯：

1. 冷藏飯兩包，每包 170g
2. 冷凍花枝丸 4 顆
3. 鴻禧菇一包
4. 蒜頭 10 瓣
5. 咖哩雞肉調理包兩包，每包 200g
6. 冷藏帶骨雞腿肉一隻 400g
7. 山藥 200g

特別說明：
雞腿肉去骨剪小塊後再料理；若不合適，請推薦安全替代做法。
```

完整食材範本請見[入門使用者指南](basic-user-guide.md)。建立新設備 Skill、來源裁決與輸出規則請見[進階使用者指南](advanced-user-guide.md)。

在 CulinaForge repository 內產生食譜卡時，完成的 Markdown、PDF、PNG 一律寫入 `output/recipe-card/`，暫存檔只寫入 `tmp/`；同名檔案依進階指南加入時間戳記，不得覆蓋。

## 更新

專案安裝執行 `git pull` 即會使用新版。個人安裝則在更新 repository 後重新複製每個完整 Skill 目錄。

## 官方文件

- [OpenAI：Build skills](https://developers.openai.com/codex/skills)
