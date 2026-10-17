# FreqUI 中文版（FreqUI Chinese Localization Fork）

基於官方 [freqtrade/frequi](https://github.com/freqtrade/frequi) **3.1.3**（tag `f2d57c3efd55d9fb66e60b2dfc2e650b6b601185`）的繁體中文中文化 fork，使用 **vue-i18n** 架構。

> FreqUI upstream 無任何 i18n（全英文）。本 fork 為官方原始碼加入完整 i18n 架構與中文語系，長時間使用、可隨官方升級同步維護。

## 功能

- ✅ vue-i18n（v11）i18n 架構：`locale='zh'`、`fallbackLocale='en'`
- ✅ 中文語系：`src/src/locales/zh.ts`（完整繁中介面）
- ✅ 英文語系（官方原文）：`src/src/locales/en.ts`
- ✅ 全部主要頁面已中文化（登入 / 交易 / 儀表盤 / 圖表 / 回測 / 分析 / 設定 / 資料下載…）

## 中文化範圍（分批完成）

| 批次 | 內容 |
|---|---|
| 第一批 | 導航列 / 交易頁 / 交易表格 / 圖表工具列 / 儀表盤 |
| 第二批 | 頁尾 / PeriodBreakdown / 機器人比較 / 機器人控制 / 狀態列 / 日誌 / 首頁 / 交易對摘要 |
| 第三批 | 交易明細 / 餘額 / 盈虧 / 強制平倉/進場 / PairLock / 機器人效能 / 設定頁 |
| 第四批 | 交易操作 / 交易導覽 / 實時交易對 / 行動版交易列表 / 圖表檢視 / 機器人列表 |
| 第五批 | 登入 / 時間範圍 / 回測 / 資料下載 / 前瞻與遞迴分析 / Pairlist 設定 / FreqAI 模型 / 策略參數 / 回測歷史與結果 / Plot Configurator 全系列 / 圖表標題 |

## 使用方法

```bash
# 1. 安裝依賴
npm install

# 2. 建置（產出 dist/）
npm run build

# 3. 部署到 freqtrade（取代 installed/，備份原版後再覆蓋）
# 原版備份：freqtrade/rpc/api_server/ui/installed.bak_zh_YYYYMMDD/
Copy-Item dist\* freqtrade\rpc\api_server\ui\installed\ -Recurse -Force
```

部署後瀏覽器 **Ctrl+F5**（清快取）即可看到中文介面。

## Git 歷史

- `8808528` 第一批（導航/交易/儀表盤）
- `1d3f443` 第二批（頁尾/日誌/控制/首頁）
- `59e1c20` 移除 CI workflow 追蹤（OAuth token 無 workflow scope，本地保留檔案）
- `54bdc15` 第三批（交易核心/設定）
- `12d8657` 第四批（交易對/歷史/圖表頁）
- `446af0c` 第五批（回測/分析/設定/登入/Plot Configurator/圖表標題）

## 升級同步（官方 frequi 新版本）

1. 拉取官方新版原始碼（對應 freqtrade 版本）
2. 套用本 fork 的 i18n 改造（main.ts + locales/ + 各元件 `t()` 改動）
3. `npm run build` 後重新部署

## 貢獻

歡迎加入中文化工作：

1. Fork 本 repo
2. 在 `src/src/locales/zh.ts` 補充翻譯 key
3. 將元件中的硬編碼英文改為 `t('namespace.key')`
4. 送出 Pull Request

翻譯 key 命名規範：`namespace.key`（如 `nav.trade`、`trade.openTrades`、`table.pair`）。

## License

同官方 [freqtrade/frequi](https://github.com/freqtrade/frequi)（GPL-3.0）。
