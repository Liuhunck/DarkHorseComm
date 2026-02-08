# Community（MemeHub）页面与路由说明

路由文件位置：`community/frontend/src/router/index.js`

> 说明：部分页面在路由 `meta.requiresAuth = true` 时需要已登录（项目通过全局导航守卫拦截，并触发登录弹窗）。

## 路由总览

| Path                 | Name                 | View                   | requiresAuth | 说明                           |
| -------------------- | -------------------- | ---------------------- | -----------: | ------------------------------ |
| `/`                  | `home`               | `HomeView.vue`         |           否 | 主页面 / 信息流入口            |
| `/search`            | `SearchView`         | `SearchView.vue`       |           否 | 搜索用户/内容                  |
| `/audit`             | `AuditView`          | `AuditMeme.vue`        |           否 | 审核/审计相关页面（模因审核）  |
| `/create-meme`       | `CreateMeme`         | `CreateMeme.vue`       |           否 | 创建模因                       |
| `/notification`      | `Notification`       | `NotificationView.vue` |           否 | 消息通知                       |
| `/chat`              | `Chat`               | `ChatView.vue`         |           是 | 私信与 C2C 交易入口            |
| `/gamification`      | `GamificationCenter` | `CheckView.vue`        |           是 | 游戏化中心（签到/任务/奖励等） |
| `/profile/:id`       | `Profile`            | `ProfileView.vue`      |           是 | 个人主页（动态路由）           |
| `/meme/:id`          | `MemeDetail`         | `MemeDetailView.vue`   |           是 | 模因详情页（动态路由）         |
| `/discover`          | `Discover`           | `DiscoverView.vue`     |           否 | 发现页（含 tab/筛选入口）      |
| `/leaderboard`       | `Leaderboard`        | `LeaderboardView.vue`  |           否 | 排行榜                         |
| `/achievements`      | `Achievements`       | `AchievementsView.vue` |           是 | 成就中心                       |
| `/price-alert`       | `PriceAlert`         | `PriceAlertView.vue`   |           是 | 价格预警                       |
| `/compare`           | `Compare`            | `CompareView.vue`      |           否 | 对比分析                       |
| `/voting`            | `Voting`             | `VotingView.vue`       |           否 | 社区投票                       |
| `/creator-dashboard` | `CreatorDashboard`   | `CreatorDashboard.vue` |           是 | 创作者面板                     |
| `/watchlist`         | `Watchlist`          | `WatchlistView.vue`    |           是 | 自选列表                       |

## 页面说明（简述）

- **HomeView**：社区主入口，承载主要内容浏览。
- **SearchView**：搜索入口，通常用于找用户/内容并跳转。
- **CreateMeme**：创建模因的表单/上传页面，提交后回到主页。
- **MemeDetailView**：模因详情页，展示详情、走势（Kline）、互动等。
- **ChatView**：私信与 C2C 交易，进入该页时会触发未读与待处理提醒刷新。
- **CheckView（Gamification）**：游戏化中心，含签到、任务引导、奖励抽取等；内部包含路由跳转/滚动定位逻辑。
- **Discover/Leaderboard/Compare/Voting**：围绕“发现/排行/分析/投票”的内容聚合页面。
- **ProfileView**：个人主页（动态 `:id`），包含用户信息与行为。

## 与 DEX 的跳转关系

- Community 与 DEX 通常作为 **两个独立站点** 部署。
- 在“从社区进入交易”场景中，通常会从 Community 的某个页面（例如 Meme 详情页/价格相关页面）跳转到 DEX：
    - DEX 详情页路由为 `/token/:address`，只需要传入 token 合约地址即可。
- 反向跳转（从 DEX 回到 Community）在 DEX 端已提供按钮实现（详见 DEX 文档）。
