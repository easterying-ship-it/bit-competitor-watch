# 券商竞品宣发监控 · BIT

头部交易所(Bitget / Binance / OKX / Gate / Bybit / HTX)美股/券商业务的**宣发手段 + 在跑活动**动态监控看板,供 BIT 美股运营对齐借鉴。

- **看板**:`index.html`(单文件,数据驱动,打开即用)
- **在线地址**:Cloudflare Pages 自动部署(每次 push 触发)
- **刷新**:`/schedule` 远程 agent 每日跑,改 `index.html` 里的 `DATA` 块后 `git push`

## 远程 agent 每日更新协议

只改 `index.html` 中 `// ===== DATA(每次刷新只改这块) =====` 与 `// ===== RENDER =====` 之间的 `DATA` 对象,**不动模板和 RENDER 逻辑**。

字段说明:
- `range` / `updated` / `next`:数据窗口、更新日、下次刷新文案。
- `highlights[]`:本期变化,`tag` ∈ `t-new`(NEW红) / `t-up`(趋势黄) / `t-info`(待核实蓝)。把**本期新发现**放最上面。
- `competitors[]`:逐家。`status` ∈ `live`(绿) / `soon`(黄) / `watch`(灰)。
  - `promo[]` 宣发手段、`activities[]` 在跑活动、`xsig` X 信号(远程抓不到登录态的标 🔒)、`align` BIT 可对齐。
- `techniques[]`:宣发手段矩阵,每家取值 `1`(在用) / `0`(未用) / `"?"`(待抓取)。
- `actions[]`:给 BIT 的借鉴动作。

### 信源(远程可达)
1. 官方活动页:`binance.com/activity`、`bitget.com` promotions、OKX/Gate/Bybit/HTX 活动中心
2. 网络新闻 + KOL 解读(WebSearch)
3. 网络流传的 X 内容(官推转发、截图报道)

### 信源(远程不可达,需本地交互补)
- X 官推原帖发布节奏、置顶、预告
- 评论区真实互动 / 水军节奏 / 转发量

这些字段在 `xsig` 里以 🔒 标记,等本地登录 X 后人工刷新。

> **重要**:`xsig` 里已经是 `📡 X实抓` 开头的内容,是本地登录 X 后人工抓的真实数据。**远程 agent 不要覆盖、不要删除、不要改写这些 📡 字段**(你没有 X 登录态,改了就是退化)。只有当你从网络公开信源拿到更新的官推动态时,可以**追加**,但保留 📡 标注。仍是 🔒 的字段才是待补的。

### 输出纪律
- 只写**核实到的**;没核实的留 `待抓取` / `"?"`,**不要编**。
- 每条尽量可溯源(报道链接 / 官推 / 活动页)。
- 比上次有变化的,进 `highlights`。
