# MultiBaseOrderBookDEXVaultLevels 接口文档（集成向）

本文档面向“前端/服务端集成合约”的开发者，按 **可直接调用的对外接口** 说明 `MultiBaseOrderBookDEXVaultLevels` 的用法与注意事项。

- 合约源码：`contracts/contracts/MultiBaseOrderBookDEXVaultLevels.sol`
- 更详细的逐函数实现/状态变更说明（更偏审计/阅读源码）：`contracts/docs/MultiBaseOrderBookDEXVaultLevels.md`

## 1. 核心概念与计量单位

- **quoteToken**：统一的计价币（如 USDT）。所有买入成本/卖出收入以 quote 计。
- **baseToken**：支持多个 base（如 BTC/ETH…），每个 base 有独立订单簿。
- **PRICE_SCALE = 1e18**：价格缩放。
    - `price` 含义：`1 base` 需要多少 `quote`，再乘以 `1e18`。
    - 即：`price = (quote / base) * 1e18`。
- **数量单位**：
    - `amountBase/filledBase/remainingBase`：base token 最小单位（如 wei）。
    - `quoteBalance/lockedQuote`：quote token 最小单位。

## 2. 资金模型（Vault / 内部账本）

- 用户先 `deposit*` 把 token 转入合约，合约维护内部余额：
    - `quoteBalance[trader]`
    - `baseBalance[trader][base]`
- 撮合成交时不做 ERC20 转账，只修改内部账本。
- 只有 `deposit* / withdraw*` 会发生实际 ERC20 转账。

## 3. Events（事件）

- `BaseTokenSupported(baseToken, decimals)`：新增支持的 base。
- `Deposited(trader, token, amount)`：充值（base 或 quote）。
- `Withdrawn(trader, token, amount)`：提现（base 或 quote）。
- `LimitOrderPlaced(orderId, trader, side, price, amountBase)`：挂限价单。
- `OrderCancelled(orderId, trader)`：撤单。
- `Trade(makerOrderId, maker, taker, takerSide, price, amountBase)`：撮合成交。

## 4. Errors（常见错误）

- `UnsupportedBaseToken()`：base 未被支持。
- `InvalidAmount()`：数量为 0 或换算后为 0。
- `InvalidPrice()`：价格为 0。
- `NotOwner()`：非订单 owner 试图操作。
- `NotActive()`：订单非 active。
- `InsufficientBalance()`：内部余额不足 / 锁定 quote 不足。
- `TransferFailed()`：ERC20 转账失败。

## 5. 对外接口（Functions）

### 5.1 管理与枚举

- `supportBaseToken(address base) external onlyOwner`
    - 作用：将某个 base token 加入支持列表。
    - 备注：会读取 `decimals()` 并缓存；重复调用对已支持 base 幂等。

- `getSupportedBases() external view returns (address[] memory)`
- `supportedBasesLength() external view returns (uint256)`
- `supportedBaseAt(uint256 index) external view returns (address)`

### 5.2 充值 / 提现（Vault）

- `depositBaseFor(address base, uint256 amount) public`
    - 前置：base 必须已支持；`amount > 0`；用户需先对合约 `approve`。
    - 效果：`transferFrom(user -> contract)`，并增加 `baseBalance[user][base]`。

- `withdrawBaseFor(address base, uint256 amount) public`
    - 前置：base 必须已支持；`amount > 0`；`baseBalance` 足够。
    - 效果：减少内部余额并 `transfer(contract -> user)`。

- `depositQuote(uint256 amount) external`
- `withdrawQuote(uint256 amount) external`
    - 逻辑与 base 类似，只是 token 固定为 `quoteToken`。

### 5.3 只读查询

- `getMyOpenOrdersFor(address base) external view returns (OrderViewMulti[] memory)`
- `getOpenOrdersOfFor(address trader, address base) public view returns (OrderViewMulti[] memory)`
    - 返回：用户在该 base 下仍 active 且未完全成交的订单列表（包含剩余数量）。

- `getLastPriceFor(address base) external view returns (uint256)`
    - 返回：该 base 最近一次成交价（单位 1e18）。

- `getOrderBookDepthFor(address base, uint256 topN) public view returns (uint256[] bidPrices, uint256[] bidSizes, uint256[] askPrices, uint256[] askSizes)`
    - `topN=0` 会默认取 10。
    - `bidSizes/askSizes` 为每档价位聚合的 `totalRemainingBase`。

### 5.4 交易接口（Taker）

- `marketBuyFor(address base, uint256 maxQuoteIn) external`
    - 含义：用最多 `maxQuoteIn` 的 quote，按当前最优卖盘逐档吃单，直到 quote 用完或卖盘耗尽。
    - 前置：`quoteBalance[msg.sender] >= maxQuoteIn`。

- `marketSellFor(address base, uint256 amountBase) external`
    - 含义：卖出 `amountBase` 的 base，按当前最优买盘逐档吃单，直到 base 卖完或买盘耗尽。
    - 前置：`baseBalance[msg.sender][base] >= amountBase`。

### 5.5 挂单接口（Maker，限价单）

- `limitBuyFor(address base, uint256 price, uint256 amountBase) external returns (uint256 orderId)`
    - 含义：挂买单（BUY）。
    - 资金：会先按 `price` 估算需要锁定的 quote（`quoteToLock`），并从 `quoteBalance` 中扣除锁定。
    - 之后会尝试对该 base 进行撮合（`_tryMatch(base)`）。

- `limitSellFor(address base, uint256 price, uint256 amountBase) external returns (uint256 orderId)`
    - 含义：挂卖单（SELL）。
    - 资金：会先从 `baseBalance` 中扣除 `amountBase`（作为卖单库存锁定）。
    - 之后同样触发撮合。

### 5.6 撤单

- `cancelOrder(uint256 orderId) external`
    - 限制：只能撤自己的订单；订单必须 `active`。
    - BUY 订单：退回剩余 `lockedQuote`。
    - SELL 订单：退回剩余 `remainingBase`。

## 6. 典型集成流程（建议）

1. `supportBaseToken(base)`（部署后由 owner 执行一次）
2. 用户对合约 `approve`（base 或 quote）
3. `depositQuote` / `depositBaseFor`
4. 使用 `getOrderBookDepthFor` / `getLastPriceFor` 拉行情
5. 下单：`limitBuyFor` / `limitSellFor` 或 `marketBuyFor` / `marketSellFor`
6. 查询：`getMyOpenOrdersFor`
7. 提现：`withdrawQuote` / `withdrawBaseFor`
