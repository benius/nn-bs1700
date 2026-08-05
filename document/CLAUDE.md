# 在 Claude Code 或 Claude Cowork 安裝與使用 CulinaForge Skills

CulinaForge 的設備 Skills 位於 `.agents/skills/`。每個 Skill 只包含一台設備；Claude 可在同一個食材提示中同時使用多個 Skill，整合設備分工與料理時間線。

## Claude Code

先取得 repository：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

### 專案安裝

macOS／Linux：

```bash
mkdir -p .claude/skills
cp -R .agents/skills/cooking-with-nn-bs1700 .claude/skills/
cp -R .agents/skills/cooking-with-rb-2232h .claude/skills/
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force .claude/skills | Out-Null
Copy-Item -Recurse .agents/skills/cooking-with-nn-bs1700 .claude/skills/
Copy-Item -Recurse .agents/skills/cooking-with-rb-2232h .claude/skills/
```

若希望所有專案都能使用，將目的地改成 `$HOME/.claude/skills`。請擇一安裝範圍，並完整複製 `SKILL.md`、`agents/` 與 `references/`。

### 使用

Claude Code 可用 `/skill-name`，或直接在自然語言中指定多個 Skill：

```text
請使用 cooking-with-rb-2232h 與 cooking-with-nn-bs1700。
我有冷藏飯兩包、冷藏帶骨雞腿肉 400g、咖哩調理包兩包、山藥 200g、鴻禧菇一包與冷凍花枝丸 4 顆，請做三人份咖哩飯。沒有重量的食材依一般情況估算，並說明兩台設備的分工。
```

## Claude Cowork

每個設備 Skill 分別製作 ZIP，ZIP 根目錄必須保留該 Skill 的目錄名稱與完整內容。例如：

```bash
cd .agents/skills
zip -r ../../cooking-with-nn-bs1700.zip cooking-with-nn-bs1700
zip -r ../../cooking-with-rb-2232h.zip cooking-with-rb-2232h
cd ../..
```

依序上傳並啟用需要的 Skills。產生食譜時，在提示中寫出每個 Skill 名稱、用餐人數、餐點名稱、所有食材與特別說明。

完整提示範本請見[入門使用者指南](basic-user-guide.md)；建立新設備 Skill 與輸出規則請見[進階使用者指南](advanced-user-guide.md)。在可寫入 CulinaForge repository 的工作階段中，完成檔案放到 `output/recipe-card/`，暫存檔只放 `tmp/`。

## 更新

更新 repository 後重新複製或打包每個完整 Skill。不要只更新 `SKILL.md` 而漏掉 `references/`。

## 官方文件

- [Anthropic：Extend Claude with skills](https://code.claude.com/docs/en/skills)
- [Anthropic：Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude)
