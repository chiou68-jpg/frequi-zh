# FreqUI 中文版（FreqUI Chinese Localization Fork）

基於官方 [freqtrade/frequi](https://github.com/freqtrade/frequi) **3.1.3** 的繁體中文中文化 fork，使用 **vue-i18n** 架構。

> FreqUI upstream 無任何 i18n（全英文）。本 fork 為官方原始碼加入完整 i18n 架構與中文語系，長時間使用、可隨官方升級同步維護。

## 功能

- ✅ vue-i18n（v11）i18n 架構：`locale='zh'`、`fallbackLocale='en'`
- ✅ 中文語系：`src/src/locales/zh.ts`
- ✅ 英文語系（官方原文）：`src/src/locales/en.ts`
- ✅ 已中文化核心頁面：導航列 / 交易頁 / 儀表盤 / 交易表格 / 圖表工具列

## 已中文化範圍（第一批）

| 元件 | 內容 |
|---|---|
| `NavBar.vue` | 導航列（交易/儀表盤/圖表/日誌/設定/登出/佈局鎖定） |
| `pages/trade.vue` | 交易頁（多窗格/持倉/已平倉/交易明細/圖表/頁籤） |
| `TradeList.vue` | 交易表格（ID/交易對/數量/投入金額/開倉價/現價/盈虧/做多/做空/強制平倉對話框） |
| `CandleChartContainer.vue` | 圖表工具列（多交易對/顯示圖表區域/Heikin Ashi/繪圖配置） |
| `pages/dashboard.vue` | 儀表盤（隨時間的盈虧/機器人比較/持倉/累計盈虧/錢包歷史/已平倉/盈虧分佈/交易記錄） |

第二批（進行中）：logs / settings / index / footer / 側邊欄 Filter / PeriodBreakdown / BotComparisonList 等。

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
