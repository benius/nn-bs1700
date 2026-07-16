# Panasonic NN-BS1700 料理設定參考

這是一個由使用者自行整理的非官方專案，專門記錄 Panasonic NN-BS1700 蒸烘烤微波爐的操作方式、料理設定與實際使用經驗。內容涵蓋微波、蒸氣、烘烤、燒烤、自動行程、配件與層位，目的是讓 NN-BS1700 使用者更快找到可實作的設定，再依食材重量、厚度、溫度與個人口感調整。

本專案不是 Panasonic、台灣松下銷售股份有限公司或其關係企業製作、授權或維護的官方文件，也不代表上述公司的意見或保證。

## 使用方式

第一次使用或操作不熟悉時，請前往 Panasonic 官方[說明書、型錄下載](https://pstw.panasonic.com.tw/Consumer/Support/Download)頁面，輸入商品型號 `nn-bs1700` 即可取得官方說明書。請先閱讀最新版使用說明書，尤其是安全注意事項、可用容器、附件與層位限制。

專案內的時間與溫度是實作起點，不是對所有食材都相同的固定答案。生鮮與冷凍狀態、份量、厚度、容器、室溫及機器狀況都會影響結果。處理肉類、蛋、魚與海鮮時，請確認中心溫度及熟度符合食品安全要求；若本專案與機身標示或官方說明書不一致，以機身標示及官方文件為準。

## 使用 AI Agent Skill

本專案提供可重複使用的 [Agent Skill](.agents/skills/cooking-with-nn-bs1700/SKILL.md)，讓 AI agent 依來源優先順序，產生 NN-BS1700 的模式、功率、溫度、時間、預熱、配件、層位與完成判定。完整 skill 統一放在跨平台共用的 `.agents/skills`。

請依使用的 agent 閱讀對應文件：

| Agent | 安裝、設定與使用說明 |
| --- | --- |
| Codex | [Codex 使用指南](document/CODEX.md) |
| Claude Code／Claude Cowork | [Claude 使用指南](document/CLAUDE.md) |
| Gemini CLI／Google AI agent | [Gemini 使用指南](document/GEMINI.md) |
| GitHub Copilot | [Copilot 使用指南](document/COPILOT.md) |

想直接開始產生料理流程，或把新食譜回存到 skill 並在實際烹煮後標為實測，請閱讀[開始使用指南](document/getting-start.md)。

## 官方參考資料

- [Panasonic 台灣：NN-BS1700 產品頁](https://www.panasonic.com/tw/consumer/kitchen/microwave/nn-bs1700.html)
- [Panasonic 台灣：NN-BS1700 商品規格](https://www.panasonic.com/tw/consumer/kitchen/microwave/nn-bs1700.specs.html)
- [Panasonic 台灣：說明書、型錄下載](https://pstw.panasonic.com.tw/Consumer/Support/Download)（輸入商品型號 `nn-bs1700` 即可取得官方說明書）
- [Panasonic 台灣：相關下載使用同意條款](https://pmst.panasonic.com.tw/frontend/Service/PSPCDLAgree.aspx)
- [Panasonic 台灣：顧客服務與支援](https://www.panasonic.com/tw/support/)

官方網站可能更新頁面、檔案與使用條款，請以 Panasonic 網站目前公布的內容為準。

## 版權、商標與限定用途聲明

### 官方資料

Panasonic、國際牌及相關名稱、標誌與商標，均屬其各自權利人所有。Panasonic 官方使用說明書、食譜、圖片、圖表、產品文字及其他官方資料，其著作權與相關權利均屬 Panasonic 或原權利人所有，並**不適用**本專案的 CC0 授權。

Panasonic 的「相關下載使用同意條款」載明，官方下載內容不得任意修改、傳送或複製，且下載檔案僅得使用於該公司製造或販售的商品。本專案引用官方資料的目的，僅限於協助 **Panasonic NN-BS1700** 使用者操作該機型；不得將本專案或其中資料解讀為適用其他機型，也不得據此主張已取得官方文件的重製、改作、散布、公開傳輸或商業使用權。

「僅供 NN-BS1700 參考」本身不會免除任何著作權責任，也不能取代權利人的授權。請從官方頁面取得文件、遵守官方條款，且不要擷取、修改、重新上傳或另行散布官方文件。如本專案中的官方參考檔有任何權利疑義，請聯絡專案維護者處理下架或修正。

### 本專案自行撰寫的內容

除官方文件、商標、圖片、引用內容及其他第三方素材外，本專案自行撰寫與整理的原創內容依 [CC0 1.0 Universal](LICENSE) 提供：在法律允許的最大範圍內，作者拋棄相關著作權及鄰接權；若拋棄無效，則依 CC0 的備援授權條款處理。

CC0 不會把第三方權利一併授權給使用者，也不代表內容正確、完整、適合特定用途或不侵害他人權利。使用者仍須自行確認資料來源、官方條款及適用法律。

## 免責聲明

本專案內容按現況提供，可能包含錯誤、遺漏或尚未驗證的個人經驗。使用者應自行判斷並承擔操作、食品安全、設備損壞及其他風險。若有異常聲響、氣味、冒煙、故障或安全疑慮，請立即停止使用、拔除電源，並依官方說明書聯絡 Panasonic 或合格維修人員。
