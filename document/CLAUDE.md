# 在 Claude Code 或 Claude Cowork 安裝與使用 NN-BS1700 skill

完整 skill 位於 `.agents/skills/cooking-with-nn-bs1700`。Claude Code 使用 `.claude/skills`，Claude Cowork 則上傳包含完整 skill 目錄的 ZIP；兩者共用同一份 `SKILL.md` 與 `references/`。

## Skill metadata

Claude Code 與 Cowork 以 `SKILL.md` 的 YAML frontmatter 辨識 skill；本專案使用跨平台的 `name` 與 `description`。獨立 skill 沒有對應的 `agents/claude.yaml`，因此本專案不建立該檔。

複製目錄或製作 Cowork ZIP 時，`agents/openai.yaml` 可以保留以維持同一份跨平台 skill；Claude 會忽略它。本 skill 也不需要 Claude 專用的叫用控制或預先核准工具，所以不在共用 frontmatter 加入這些專用欄位。

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
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force .claude/skills | Out-Null
Copy-Item -Recurse .agents/skills/cooking-with-nn-bs1700 .claude/skills/
```

### 個人安裝

若希望所有專案都能使用，改複製到個人目錄。

macOS／Linux：

```bash
mkdir -p "$HOME/.claude/skills"
cp -R .agents/skills/cooking-with-nn-bs1700 "$HOME/.claude/skills/"
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME/.claude/skills" | Out-Null
Copy-Item -Recurse .agents/skills/cooking-with-nn-bs1700 "$HOME/.claude/skills/"
```

請擇一安裝範圍。若 Claude Code 啟動時 `.claude/skills` 尚不存在，建立後重新啟動一次；之後 skill 內容的修改通常可即時偵測。

### 確認與使用

在 Claude Code 輸入 `/cooking-with-nn-bs1700` 明確叫用，或以自然語言提出相符問題：

```text
/cooking-with-nn-bs1700 我有 300 g 冷凍蝦仁，想用 NN-BS1700 做到熟而不乾，請給完整操作流程。
```

```text
請使用 cooking-with-nn-bs1700，排查蒸氣變弱與爐內白粉，先說立即處置，再列清潔步驟。
```

提示中最好包含生鮮／冷藏／冷凍狀態、重量、數量、最厚處厚度、目標口感與手邊配件；資訊不足時，skill 會明列假設與不同狀態的選項。

只想用食譜名稱與主要食材取得料理流程，請閱讀[入門使用者指南](basic-user-guide.md)；需要完整指定料理條件、把新食譜回存到 skill，或在實際烹煮後標為實測，請閱讀[進階使用者指南](advanced-user-guide.md)。

## Claude Cowork

### 製作上傳檔

從 repository 根目錄製作 ZIP。ZIP 內必須保留 `cooking-with-nn-bs1700/SKILL.md`、`references/` 與其他相對路徑。

macOS／Linux：

```bash
cd .agents/skills
zip -r ../../cooking-with-nn-bs1700.zip cooking-with-nn-bs1700
cd ../..
```

Windows PowerShell：

```powershell
Compress-Archive -Path .agents/skills/cooking-with-nn-bs1700 -DestinationPath ./cooking-with-nn-bs1700.zip -Force
```

### 上傳與啟用

1. 在 Claude 開啟 **Customize > Skills**。
2. 選擇 **+ > Create skill > Upload a skill**。
3. 上傳 `cooking-with-nn-bs1700.zip`，並確認 skill 已開啟。
4. 進入 Cowork，以自然語言描述食材、保存狀態、重量、最厚處厚度、目標口感與手邊配件。

範例：

```text
使用 cooking-with-nn-bs1700：兩片冷藏豬排，每片約 180 g、厚 2 cm，想要焦香全熟。請列出配件、盤面、層位、預熱、分段時間、翻面時機與中心溫度。
```

## 更新

更新 repository 後，Claude Code 重新複製整個 skill 目錄；Cowork 則重新製作 ZIP 並上傳新版。不要只更新 `SKILL.md` 而漏掉 `references/`。

## 官方文件

- [Anthropic：Extend Claude with skills](https://code.claude.com/docs/en/skills)
- [Anthropic：Use skills in Claude（含 Cowork 上傳）](https://support.claude.com/en/articles/12512180-use-skills-in-claude)
