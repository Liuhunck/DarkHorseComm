# DarkHorse Docs

本目录用于汇总 DarkHorse 的合约接口与前端页面说明，方便开发/测试/评审快速定位。

## 目录

- [contracts 接口文档](contracts/MultiBaseOrderBookDEXVaultLevels.md)
- [contracts 并行优化说明](contracts/MultiBaseOrderBookDEXVaultLevels.parallelism.md)
- [Community 页面与路由](community/ROUTES.md)
- [DEX 前端页面说明](dex/PAGES.md)

## Community ↔ DEX 互相跳转说明

- `dex/frontend` 内置“DarkHorse Community”按钮（见 `dex/frontend/src/App.vue`），点击后会通过 `window.open(DARKHORSE_URL, ...)` 打开 Community。
    - 默认 `DARKHORSE_URL = http://localhost:3000/`，本地联调时可按你的实际端口/域名修改。
- Community 与 DEX 是两个独立的前端应用（通常分别部署）。因此 **从 Community 跳转到 DEX** 的方式一般是：
    - 直接打开 DEX 的站点 URL（例如本地 `http://localhost:<dex-port>/`），或
    - 由 Community 的导航/按钮跳到 DEX 的 URL（可在部署时配置/接入）。

> 约定：DEX 的详情页路由为 `/token/:address`，因此 Community 在跳转到 DEX 时只需要拼接 token 合约地址即可实现“从社区进入交易”。
