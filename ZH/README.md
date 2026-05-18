# 使用者可控設定層防禦模式
### User-Controlled Config Defense Patterns

> 本文件為 `../README.md` 的繁體中文對應版本，英文為 canonical（正式版本）。若翻譯有歧異以英文版為準。

一篇模式論文（pattern paper）與可部署的防禦範本，適用於暴露使用者可控 memory 與 system-context 層的 LLM 編碼代理（coding-agent）執行環境。

**狀態（Status）**：試點研究（pilot study），防禦可靠度宣稱 n=5。本文為模式論文，不是完整紅隊（red team）測試報告。

**語言（Languages）**：[English (canonical)](../README.md) · 繁體中文

**專案（Project）**：本文為長期防禦研究專案的 v1.0。啟動此專案的觸發點，是社交平台上面向非專業使用者的公開 jailbreak 教學的明顯擴散——攻擊者族群正從高技能對手，轉向「跟著步驟教學操作的商品化使用者（commodity users）」。防禦研究應跟上這個節奏。

---

## 快速上手（Quick start，30 秒）

1. 在你 LLM 代理（agent）的 context 中，找出**永遠載入的前言段（always-loaded preamble）**。（Memory index 檔？System prompt？Assistant instructions？runtime-specific 映射見 [範本](./always_loaded_guard_rules_template.md)。）
2. 把 [`always_loaded_guard_rules_template.md`](./always_loaded_guard_rules_template.md) 的防禦區塊複製到該位置最上方。
3. 閱讀 [paper §5.4](./Defense_Pattern_Paper_v1.0.md)，理解 schema lock 是**軟鎖（soft lock，行為等級）**，不是執行環境強制執行。
4. 維護你自己觀察到的具體攻擊 token 的私有威脅清單（private threat-list）——不要公開。

如果你的執行環境中找不到永遠載入表面，本模式不適用；請見範本的「Adapting to your runtime」章節。

---

## 這是什麼（What this is）

我們對某 LLM 編碼代理（coding agent）的使用者可控設定層進行了紅隊測試，使用 8 個攻擊向量。7 個在模型對齊（alignment）層被偵測或拒絕；1 個產生了部分人格漂移（partial persona shift），但 payload 未被送達——是架構的副作用（architectural side-effect），不是主動防禦。

從測試中萃取出 4 個架構洞察（architectural insights）與 1 個可部署的防禦模式。

## 這不是什麼（What this is not）

- **不是完整的紅隊測試報告。** Indirect injection（網頁／PDF／檔案內嵌）、學術 SOTA 對抗性後綴（GCG／AutoDAN 系列）、跨模態（cross-modal）payload、工具/函式呼叫操控、context-window flooding 都**不在**範圍內。OWASP LLM Top 10 的對應關係見 paper §1.5。
- **不是跨模型宣稱。** 僅測試一個模型家族。
- **不是 production-ready。** n=5，Wilson 95% CI 下界 56.6%。請於 n≥30 複測後再作為唯一防線部署。

## 內容（Contents）

| 檔案 File | 內容 |
|------|------|
| [`./Defense_Pattern_Paper_v1.0.md`](./Defense_Pattern_Paper_v1.0.md) | 主論文。威脅模型、攻擊型錄（抽象化）、4 個架構洞察、防禦模式、限制 |
| [`./always_loaded_guard_rules_template.md`](./always_loaded_guard_rules_template.md) | 獨立防禦範本，含 OpenAI Assistants API、LangChain、自製代理的執行環境適配指南 |
| [`../examples/`](../examples/) | 一份 sample 部署範例：在 context 中含防禦區塊的 memory index |
| [`../LICENSE`](../LICENSE) | CY Future Open Defense License v1.0 |
| [`../CITATION.cff`](../CITATION.cff) | GitHub-native citation metadata |
| [`../SECURITY.md`](../SECURITY.md) | Responsible disclosure 通報管道 |
| [`../CHANGELOG.md`](../CHANGELOG.md) | 版本變更紀錄 |
| [`../README.md`](../README.md) | 英文 canonical README |

## 4 個洞察，各一段話（The four insights, in one paragraph each）

1. **延遲載入（Lazy-loading）。** Memory entry 內文在 session 開始時並未載入——只載入 index 表頭與 pointer。放在 entry 內文的攻擊，必須依賴代理（agent）的 recall。
2. **Skill init ≠ Agent recall。** Skill 被啟動時載入的是它自己的設定檔，**不是** memory entries。但 main agent loop 在後續輪次仍可 recall entries。兩條路徑的信任語意（trust semantics）不同。
3. **System-context 攻擊會自我破壞（self-defeat）。** System-context 層的攻擊會觸發代理（agent）的高警戒行為狀態，連帶壓制其他同時發動的攻擊。顯眼會引來偵測。
4. **放置位置決定可靠度（Placement determines reliability）。** 永遠載入位置（memory index 表頭、system-context 檔）上的防禦規則會穩定觸發；需求載入位置（entry footer）上的規則則隨機。**放置位置比措辭重要。**

## 模式，一句話（The pattern, in one sentence）

**把防禦規則放在 memory index 表頭——永遠載入的前言段（always-loaded preamble），而不是個別 entry 的 footer。**

## 為何抽象化攻擊 token（Why we abstract attack tokens）

範本只列攻擊指示器的**類別（categories）**，不列我們實際觀察到的具體 token。公開可重用的攻擊 token，會把攻擊邊際成本壓低的幅度大於它對防禦的助益。每位實作者應自行維護環境內所觀察到的具體 token 的私有威脅清單。

## 可重現性（Reproducibility）

威脅模型與抽象化攻擊型錄足以建構可比較的測試平台（test harness）。具體攻擊內容未公開。

## License

**CY Future Open Defense License v1.0** — 詳見 [`LICENSE`](../LICENSE) 全文。

簡述：可自由使用、修改、再散布、衍生，包含商業用途。**需註明歸屬於 CY Future（Attribution required）。** 無擔保（no warranty）。鼓勵衍生作品維持開放（open derivatives encouraged），但法律上不強制要求（not legally required）。

> 註：法律文本為 License 原文，本繁中對應版本不翻譯 `LICENSE` 檔。

## 關於 CY Future（About CY Future）

CY Future 為本工作的作者與維護者（author and maintainer）。v1.0 以公開研究釋出（open research release）形式準備發布；後續版本將透過 GitHub releases 標記版本（檔名中的 `v1.0` 後綴不會就地更新——詳見 [`CHANGELOG.md`](../CHANGELOG.md)）。

如需引用、註明來源（attribution）或進行揭露（disclosure）：請開 GitHub issue，或透過 [`SECURITY.md`](../SECURITY.md) 中列出的管道聯繫。

## 引用（Citation）

```bibtex
@misc{cyfuture_user_config_defense_2026,
  title        = {Defending User-Controlled Config Against Prompt Injection:
                  Architectural Insights and the Always-Loaded Guard Rules Pattern},
  author       = {{CY Future}},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Pilot study, v1.0},
  url          = {https://github.com/LKCoffee/user-config-defense-patterns}
}
```

（發布時將 `url` 欄位置換為實際 repo URL。若引用特定版本，請使用 versioned tag。）

GitHub 原生引用 metadata 亦提供於 [`CITATION.cff`](../CITATION.cff)，供 "Cite this repository" 按鈕使用。

## 我們預期會被提出的問題——以及我們的 v2 路線圖

審閱者會對 v1.0 提出的 4 個合理問題，是 v2 的**明確目標（explicit target）**，不是被拖延的藉口：

| 審閱者問題 | v2 承諾 |
|----------------|---------------|
| 「n=5 太小」 | RCT 於 n≥30，跨多個 context-fill 比例 |
| 「Indirect injection 怎麼辦？」 | 至少對 3 種 indirect-injection 載體（網頁／PDF／結構化檔案）測試 Always-Loaded Guard Rules 模式 |
| 「為何沒有 GCG / AutoDAN 比對？」 | 對同一防禦執行 GCG（Zou et al. 2023）與 AutoDAN（Liu et al. 2023），回報遷移率（transfer rate）與防禦觸發率 |
| 「多輪只測 T1？」 | 在防禦已部署下，跑完整 T1-T7 累積鏈，回報失敗輪次（若有） |

完整 v2 路線圖見 paper §7。

## 為何要長期經營（Why long-term）

攻擊者族群已可見的轉移——從高技能對手轉為跟著公開教學操作的商品化攻擊者——意味著邊際攻擊者的技能更低、但存取能力相同。防禦研究應產出**非專業者可直接落地的部署模式**，而不只是學術發現。v1.0 是其中一例。後續版本目標一致：每一個 deliverable 都該能由非 LLM 安全研究人員直接放入正式部署。
