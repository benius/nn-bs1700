# 在 Gemini CLI／Google AI agent 安裝與使用 NN-BS1700 skill

本指南的原生 Agent Skills 安裝方式適用於 Gemini CLI。Gemini CLI 會掃描專案或個人層級的 `.agents/skills`，因此本 repository 可直接使用。Gemini 網頁版、Google AI Studio 或自行建立的 Vertex AI agent 不會因本機存在此目錄就自動載入；那些介面需依各自的系統指令或檔案檢索機制另行整合。

## Skill metadata

Gemini CLI 以 `SKILL.md` frontmatter 的 `name` 與 `description` 辨識、觸發 skill，並在啟用後取用其他內容。它沒有對應的 `agents/gemini.yaml`，因此本專案不建立該檔。`agents/openai.yaml` 是 OpenAI 產品專用的選用設定，Gemini CLI 會忽略它；保留該檔可讓同一份 skill 目錄繼續跨平台共用。

## 安裝

先取得 repository：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

### 專案安裝（建議）

完整 skill 已在 `.agents/skills/cooking-with-nn-bs1700`，從 repository 根目錄啟動 `gemini` 即可，不必複製檔案。

### 個人安裝

若希望所有 workspace 都能使用，複製到個人層級。Gemini CLI 同時接受 `~/.agents/skills` 與 `~/.gemini/skills`；以下使用可與其他 agent 共用的前者。

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

請擇一安裝範圍，避免 workspace 版覆蓋同名個人版時造成版本混淆。

## 設定與確認

Agent Skills 預設啟用。可先在系統終端確認：

```bash
gemini skills list
gemini skills enable cooking-with-nn-bs1700
```

已進入互動工作階段時，使用：

```text
/skills list
/skills reload
```

若組織管理員停用了 Agent Skills，個人設定無法繞過該政策，需聯絡管理員。

## 使用

Gemini 的 `activate_skill` 由 agent 自行呼叫；使用者不直接執行該工具。請在提示中明確寫出 skill 名稱與料理條件，並在 Gemini 要求啟用權限時同意：

```text
請使用 cooking-with-nn-bs1700。我要加熱一份 350 g 冷藏炒麵，請給模式、功率、時間、攪拌時機、容器與完成判定。
```

```text
用 NN-BS1700 處理 3 cm 厚冷凍雞胸；請先判斷解凍路徑，再列配件、層位、蒸烤設定與未達安全溫度時的補時方式。
```

為了得到可直接操作的答案，盡量提供食材狀態、重量、數量、最厚處厚度、帶骨／填餡情況、目標口感與可用配件。

只想用食譜名稱與主要食材取得料理流程，請閱讀[入門使用者指南](basic-user-guide.md)；需要完整指定料理條件、把新食譜回存到 skill，或在實際烹煮後標為實測，請閱讀[進階使用者指南](advanced-user-guide.md)。

## 更新

專案安裝執行 `git pull` 即可。個人安裝則在更新 repository 後重新複製完整 skill 目錄，讓 `SKILL.md` 與所有 `references/` 保持同一版本。

## 官方文件

- [Gemini CLI：Managing Agent Skills](https://geminicli.com/docs/cli/using-agent-skills/)
- [Gemini CLI：Activate skill tool](https://geminicli.com/docs/tools/activate-skill/)
