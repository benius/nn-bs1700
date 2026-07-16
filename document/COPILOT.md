# 在 GitHub Copilot 安裝與使用 NN-BS1700 skill

Agent Skills 可用於 Copilot cloud agent、Copilot code review、Copilot CLI、GitHub Copilot app，以及 VS Code／JetBrains 的 agent mode。完整 skill 位於 `.agents/skills/cooking-with-nn-bs1700`。

## Skill metadata

Copilot 以 `SKILL.md` frontmatter 的 `name` 與 `description` 辨識、觸發 skill，沒有對應的 `agents/copilot.yaml`，因此本專案不建立該檔。`agents/openai.yaml` 是 OpenAI 產品專用的選用設定，Copilot 會忽略它。

Copilot 可在 `SKILL.md` 使用 `allowed-tools` 預先核准工具，但本 skill 只需讀取隨附的 Markdown 參考檔，不需要預先核准 `shell` 或 `bash`，因此共用 frontmatter 刻意不加入該欄位。

## 安裝

### 專案安裝（建議）

Clone repository 後，Copilot 會從專案內的 `.agents/skills` 掃描 skill：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

在此 repository 開啟 Copilot agent mode 或啟動 Copilot CLI 即可，不必複製檔案。

### 個人安裝

若希望在所有專案使用，複製到 `~/.agents/skills`；Copilot 也接受 `~/.copilot/skills`。

macOS／Linux：

```bash
mkdir -p "$HOME/.agents/skills"
cp -R .agents/skills/cooking-with-nn-bs1700 "$HOME/.agents/skills/"
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
Copy-Item -Recurse .agents/skills/cooking-with-nn-bs1700 "$HOME/.agents/skills/"
```

### 使用 GitHub CLI 安裝（選用）

GitHub CLI 2.90.0 以上的 `gh skill` 仍屬 public preview。先預覽完整內容，再安裝到 Copilot 個人層級；因 skill 位於隱藏目錄，必須加入 `--allow-hidden-dirs`。

```bash
gh skill preview benius/nn-bs1700 .agents/skills/cooking-with-nn-bs1700/SKILL.md --allow-hidden-dirs
gh skill install benius/nn-bs1700 .agents/skills/cooking-with-nn-bs1700/SKILL.md --allow-hidden-dirs --agent github-copilot --scope user
```

請擇一安裝方式與範圍，避免同名的專案版與個人版同時存在。

## 設定與確認

skill 只需讀取隨附的 Markdown 參考檔，不要求預先核准 shell／bash，也不需要 MCP server。若 Copilot 無法找到 skill，確認下列檔案存在且名稱大小寫正確：

```text
.agents/skills/cooking-with-nn-bs1700/SKILL.md
```

Copilot CLI 可用 `/cooking-with-nn-bs1700` 明確叫用；其他介面可在提示中寫出 skill 名稱，或讓 Copilot 依 description 自動選用。

## 使用

```text
/cooking-with-nn-bs1700 我有一尾冷藏魚，最厚處 2 cm，想要蒸得濕潤。請列出前處理、水箱、容器、層位、模式、時間與完成判定。
```

```text
請使用 cooking-with-nn-bs1700，說明 NN-BS1700 出現 U50 時的立即處置、可能原因、預防方式，以及何時應停用送修。
```

提示中最好包含生鮮／冷藏／冷凍狀態、重量、數量、最厚處厚度、目標口感與手邊配件；資訊不足時，skill 會明列假設與不同狀態的選項。

想直接開始產生料理流程，或把新食譜回存到 skill 並在實際烹煮後標為實測，請閱讀[開始使用指南](getting-start.md)

## 更新

專案安裝執行 `git pull`。手動個人安裝需重新複製整個 skill 目錄；用 `gh skill` 安裝者可執行 `gh skill update cooking-with-nn-bs1700`。

## 官方文件

- [GitHub：About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [GitHub：Adding agent skills for GitHub Copilot](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills)
- [GitHub CLI：gh skill install](https://cli.github.com/manual/gh_skill_install)
