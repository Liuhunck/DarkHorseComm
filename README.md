# DarkHorse Community

DarkHorse Community: A full on-chain order book DEX built for the Pharos network. Submitted for the Order Book DEX track of GWDC2026.

[English Version / 英文版](README_EN.md)

## 项目简介

本仓库是 **DarkHorse Community / DarkHorse DEX** 的主入口，用于聚合链上合约与前端子项目（通过 Git submodule 引入）。

## 核心功能

- **链上订单簿撮合（Order Book DEX）**：限价单/市价单、撤单、撮合成交。
- **资金托管 Vault 模型**：充值/提现 + 撮合仅修改内部余额。
- **深度与行情**：按价位聚合的深度查询（levels）、最近成交价等。
- **一键开发/测试/部署**：合约编译、单测、部署与验证脚本；配套最小测试前端与业务前端。

## 仓库结构（Submodules）

本仓库主要代码都在以下 submodule 中：

- **contracts**：Pharos 链上合约开发 / 部署 / 验证代码（Hardhat）。
- **test**：合约测试用的简易前端（用于快速连链、调用合约）。
- **community**：**DarkHorse Community** 前端代码。
- **dex**：**DarkHorse DEX** 前端代码。

## 文档（Docs）

更多合约与前端说明请看：

- 总览：[`docs/README.md`](docs/README.md)
- 合约接口文档：[`docs/contracts/MultiBaseOrderBookDEXVaultLevels.md`](docs/contracts/MultiBaseOrderBookDEXVaultLevels.md)
- Community 路由与页面：[`docs/community/ROUTES.md`](docs/community/ROUTES.md)
- DEX 页面说明：[`docs/dex/PAGES.md`](docs/dex/PAGES.md)

## 技术栈说明

- **智能合约**：Solidity + Hardhat（包含部署/verify 脚本）
- **前端**：Vue 3 + Vite
    - `community`：Vue 3 / Vite / Tailwind CSS / Vue Router
    - `dex/frontend`：Vue 3 / Vite / ethers.js
    - `test/frontend`：Vue 3 / Vite / ethers.js
- **链上交互**：ethers.js
- **包管理**：npm（各子项目内各自维护依赖）

## 快速开始（5 分钟内能跑起来）

前提：已安装 **Git** 与 **Node.js**（建议 Node 20+；`dex/frontend` 在 `package.json` 中声明了 Node 版本要求）。

### 1) 克隆并拉取 submodules

```bash
git clone --recurse-submodules <THIS_REPO_URL>
cd DarkHorseComm
```

如果你已克隆过仓库：

```bash
git submodule update --init --recursive
```

### 2) 运行 DarkHorse Community 前端（最快）

```bash
cd community
npm install
npm run dev
```

### 3) 可选：运行 DarkHorse DEX 前端

```bash
cd dex/frontend
npm install
npm run dev
```

### 4) 可选：运行合约测试简易前端

```bash
cd test/frontend
npm install
npm run dev
```

> 上面任意一个前端 `npm run dev` 成功启动即表示“5 分钟内可运行”。

## 配置说明

### contracts（合约）

合约子项目依赖环境变量（在 `contracts/hardhat.config.js` 与 `contracts/scripts/*` 中读取）：

- `PHAROS_ATLANTIC_URL`：Pharos Atlantic RPC
- `TEST_ACCOUNT_0`：部署/测试用私钥
- （可选）`ETH_SEPOLIA_URL`：如需切换/测试 sepolia 网络
- （可选）`ETHERSCAN_API_KEY`：如需 etherscan 风格接口 key（Pharos 使用 customChains）
- （可选）`PHAROS_VERIFY_AUTH`：如果 verify API 需要鉴权

说明：`contracts` 使用了 `@chainlink/env-enc`，你可以选择直接在 Shell 里导出环境变量，或按该工具的方式管理加密环境文件（见 `contracts/.env.enc`）。

### community（前端）

社区前端可选配置：

- `VITE_SERVER_IP`：后端服务地址（未配置时默认 `http://localhost:3000`，用于头像等资源路径）

### dex/test（前端）

如需连到实际链上合约地址，通常会在前端项目内通过 `.env`（`VITE_*`）或配置文件维护；具体以各子项目 README/代码为准。

## 使用示例

### 合约编译与测试

```bash
cd contracts
npm install
npm run compile
npm test
```

### 部署到 Pharos Atlantic（示例：按价位分桶订单簿合约）

```bash
cd contracts
set PHAROS_ATLANTIC_URL=<YOUR_RPC_URL>
set TEST_ACCOUNT_0=<YOUR_PRIVATE_KEY>
npm run deploy:orderbook:levels -- --quote <QUOTE_TOKEN_ADDRESS>
```

### 拉取 submodule 最新代码（跟随 `.gitmodules` 指定分支）

```bash
git submodule update --remote --merge
```

## 常见问题（FAQ）

### 1) 为什么我克隆后目录是空的？

你可能没有拉取 submodule：

```bash
git submodule update --init --recursive
```

### 2) `npm install` 失败或 Node 版本不匹配怎么办？

- 请升级到 Node 20+（`dex/frontend` 对 Node 版本有要求）。
- 建议删除对应子项目下的 `node_modules` 后重装。

### 3) 部署脚本提示缺少 `PHAROS_ATLANTIC_URL` 或 `TEST_ACCOUNT_0`

这是必须的环境变量；先在当前 Shell 设置好再运行脚本。

## 联系方式

- 建议通过本仓库的 GitHub Issues 反馈问题与讨论需求。
- 相关代码仓库维护者（submodule 来源）：
    - https://github.com/Liuhunck
    - https://github.com/qinyh10300
    - https://github.com/Yyhhh6
