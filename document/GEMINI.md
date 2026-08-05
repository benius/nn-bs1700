# 在 Gemini CLI／Google AI agent 安裝與使用 CulinaForge Skills

Gemini CLI 會掃描專案或個人層級的 `.agents/skills`。CulinaForge 已將每台烹調設備放在獨立 Skill 中，產生食譜時可由 Gemini 同時啟用一個或多個 Skill。

## 安裝

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

### 專案安裝

從 repository 根目錄啟動 Gemini CLI，即可使用 `.agents/skills/` 內的 Skills。

### 個人安裝

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

請擇一安裝範圍，避免 workspace 版與個人版同名 Skill 混淆。

## 確認與使用

```bash
gemini skills list
gemini skills enable cooking-with-nn-bs1700
gemini skills enable cooking-with-rb-2232h
```

Gemini 的 Skill 由 agent 啟用；提示中直接寫出所有設備 Skill 與食材內容：

```text
請使用 cooking-with-rb-2232h 與 cooking-with-nn-bs1700，依下列食材設計三人份咖哩飯：冷藏飯兩包各 170g、冷藏帶骨雞腿肉 400g、咖哩雞肉調理包兩包各 200g、山藥 200g、鴻禧菇一包、蒜頭 10 瓣、冷凍花枝丸 4 顆。沒有重量的食材依一般情況估算，並安排兩台設備的同步時間線。
```

完整提示範本請見[入門使用者指南](basic-user-guide.md)；從設備商使用手冊建立新 Skill 請見[烹調設備 Agent Skill 製作指南](create-agent-skill-for-cooking-appliance.md)，多設備分工與輸出規則請見[進階使用者指南](advanced-user-guide.md)。在 CulinaForge repository 內產生的 Markdown、PDF、PNG 放在 `output/recipe-card/`，暫存檔只放 `tmp/`。

## 更新

專案安裝執行 `git pull`。個人安裝則重新複製每個完整 Skill 目錄。

## 官方文件

- [Gemini CLI：Managing Agent Skills](https://geminicli.com/docs/cli/using-agent-skills/)
- [Gemini CLI：Activate skill tool](https://geminicli.com/docs/tools/activate-skill/)
