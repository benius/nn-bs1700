# 在 GitHub Copilot 安裝與使用 CulinaForge Skills

CulinaForge 的設備 Skills 位於 `.agents/skills/`。每個 Skill 只包含一台設備；Copilot 產生食譜時可以同時使用多個 Skill，並由各 Skill 分別提供其設備設定與限制。

## 安裝

Clone repository 後，在專案中開啟 Copilot agent mode 或啟動 Copilot CLI：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

若希望在所有專案使用，複製需要的完整設備 Skill 到 `~/.agents/skills`：

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

請擇一安裝範圍，並保持每個 Skill 的 `SKILL.md`、`agents/` 與 `references/` 完整。

## 確認與使用

Copilot CLI 可用 `/skill-name`；其他介面可在提示中直接寫出 Skill 名稱：

```text
請使用 cooking-with-rb-2232h 與 cooking-with-nn-bs1700，依下列食材設計三人份咖哩飯：冷藏飯兩包各 170g、冷藏帶骨雞腿肉 400g、咖哩雞肉調理包兩包各 200g、山藥 200g、鴻禧菇一包、蒜頭 10 瓣、冷凍花枝丸 4 顆。沒有重量的食材依一般情況估算；雞腿去骨剪小塊若不合適，請改用安全做法。
```

完整提示範本請見[入門使用者指南](basic-user-guide.md)；建立新設備 Skill 與輸出規則請見[進階使用者指南](advanced-user-guide.md)。在 CulinaForge repository 內產生食譜檔時，Markdown、PDF、PNG 一律放入 `output/recipe-card/`，暫存檔只放 `tmp/`。

## 更新

專案安裝執行 `git pull`。個人安裝則重新複製每個完整 Skill 目錄。

## 官方文件

- [GitHub：About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [GitHub：Adding agent skills for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills)
