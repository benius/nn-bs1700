# 在 Codex 安裝與使用 NN-BS1700 skill

本指南適用於 ChatGPT 桌面版中的 Codex、Codex CLI 與 Codex IDE extension。skill 不需要 API key、MCP server 或額外套件。

## 平台設定檔

`agents/openai.yaml` 是 OpenAI 產品專用的選用設定，不屬於跨平台 Agent Skills 核心規格。本專案只設定三個介面欄位：

- `display_name`：顯示為「Panasonic NN-BS1700 料理指南」。
- `short_description`：顯示簡短的蒸、烤、微波料理說明。
- `default_prompt`：插入明確叫用 `$cooking-with-nn-bs1700` 的範例提示。

本檔沒有設定圖示、品牌色、工具依賴或叫用政策；未設定 `allow_implicit_invocation` 時使用預設值 `true`，所以 Codex 仍可依 `SKILL.md` 的 `description` 自動選用 skill。Claude、Gemini 與 Copilot 不讀取此檔。

## 安裝

先取得 repository：

```bash
git clone https://github.com/benius/nn-bs1700.git
cd nn-bs1700
```

### 專案安裝（建議）

Repository 已把完整 skill 放在 `.agents/skills/cooking-with-nn-bs1700`。從 repository 根目錄啟動 Codex 即可；不必再複製檔案。

### 個人安裝

若希望在所有專案使用，從 repository 根目錄複製到個人 skill 目錄。請擇一安裝範圍，避免同名的專案版與個人版同時出現在選單。

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

## 設定與確認

1. 在 Codex CLI 或 IDE 執行 `/skills`，或輸入 `$` 後搜尋 `cooking-with-nn-bs1700`。
2. ChatGPT 桌面版可從側邊欄的 Skills 查看。`agents/openai.yaml` 會提供顯示名稱、簡短說明與預設提示。
3. 新安裝後若沒有出現，重新啟動 Codex。

## 使用

明確指定 skill：

```text
$cooking-with-nn-bs1700 我有兩片冷藏 2 公分厚雞胸，想要濕潤全熟，該怎麼操作？
```

也可以直接描述 NN-BS1700 料理問題，讓 Codex 依 description 自動選用：

```text
NN-BS1700 要怎麼烤 3 公分厚的冷凍鮭魚？請列出解凍、配件、層位、預熱、時間與完成判定。
```

若未提供保存狀態、重量或厚度，skill 會列出必要假設或分別提供生鮮、冷藏、冷凍方案。肉、魚、蛋與剩食仍應依中心溫度或完成狀態驗收。

想直接開始產生料理流程，或把新食譜回存到 skill 並在實際烹煮後標為實測，請閱讀[開始使用指南](getting-start.md)。

## 更新

專案安裝執行 `git pull` 即會使用新版。個人安裝則在更新 repository 後，重新複製整個 `cooking-with-nn-bs1700` 目錄，確保 `SKILL.md` 與 `references/` 同步。

## 官方文件

- [OpenAI：Build skills](https://developers.openai.com/codex/skills)
