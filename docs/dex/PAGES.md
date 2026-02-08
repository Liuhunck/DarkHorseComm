# DEX 前端页面说明

路由文件位置：`dex/frontend/src/router/index.js`

## 路由与页面

| Path              | Name    | View            | 说明                                                            |
| ----------------- | ------- | --------------- | --------------------------------------------------------------- |
| `/`               | `home`  | `HomeView.vue`  | 市场列表入口（Grid/List 切换），选择交易对进入详情              |
| `/token/:address` | `token` | `TokenView.vue` | 交易对详情页（K线、订单簿、下单面板、挂单列表、资产与金库信息） |

## 页面说明

### HomeView（`/`）

- **目的**：展示可交易 token/市场列表。
- **核心组件**：
    - `Coins.vue`：以网格方式展示 token（常见入口）。
    - `MarketList.vue`：以列表方式展示 token/市场。
- **交互**：支持 `Grid/List` 视图切换。

### TokenView（`/token/:address`）

- **目的**：围绕某个 base token 地址完成“看盘 + 下单 + 查仓位/金库”。
- **核心模块（页面布局）**：
    - `KlineChart`：K 线/价格走势展示。
    - `OrderBook`：买卖盘深度。
    - `TradingPanel`：交易操作（钱包、下单等）。
    - `LimitOrder`：限价单与相关操作。
    - `DexAssetsCard`：资产概览。
    - `TokenVaultCard`：金库/余额相关信息。
- **链上读取**：
    - 会读取 base token 的 `symbol/decimals`（ERC20）
    - 会从 DEX 合约侧读取 `quoteToken/quoteDecimals`（通过 `callDex(...)`）

## 与 Community 的互相跳转

- DEX 顶部栏包含“DarkHorse Community”按钮（见 `dex/frontend/src/App.vue`）。
    - 点击后会 `window.open(DARKHORSE_URL, ...)` 打开 Community 站点。
    - 默认 `DARKHORSE_URL = http://localhost:3000/`，本地联调/部署时建议改成真实 Community 地址。
- Community 侧如需跳转到 DEX，建议直接打开 DEX 的 URL，并拼接 DEX 路由：`/token/:address`。
