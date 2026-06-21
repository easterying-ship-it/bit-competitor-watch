# 券商竞品宣发监控 · BIT

头部交易所(Bitget / Binance / OKX / Gate / Bybit / HTX / Kraken / Coinbase,**会随新入场者增补**)美股/券商业务的**宣发手段 + 在跑活动**动态监控看板,供 BIT 美股运营参考。BIT 自己的对齐/目标由市场部讨论,看板不再写「BIT 可对齐」建议。

- **看板**:`index.html`(单文件,数据驱动,打开即用)
- **在线地址**:GitHub Pages 自动部署(每次 push 触发)
- **刷新**:claude.ai 远程 routine `trig_018u4hghswByy4Tvhm2uEsF3` 每小时跑(cron `0 * * * *`,sonnet-4-6),改 `index.html` 的 `DATA` 块后 `git push`(无变化不提交)。用 `RemoteTrigger` 工具管它。

> **⚠️ push 通道(2026-06-01 踩坑修复)**:远程 routine 能 push 的前提是 **Claude 的 GitHub App 已"安装"到本仓库**(github.com/apps/claude → Install,选本 repo + write code 权限;`installation_id=137201342`)。**仅在 claude.ai Connectors 里"连接"GitHub(OAuth)不够**——那只够读/附文件,routine 推不上去会静默失败、把更新烂在运行结果里(2026-05-31~06-01 期间 30+ 次自动运行零提交即此因)。若哪天又不推了,先查这个 App 安装是否还在。

## 远程 agent 每日更新协议

只改 `index.html` 中 `// ===== DATA(每次刷新只改这块) =====` 与 `// ===== RENDER =====` 之间的 `DATA` 对象,**不动模板和 RENDER 逻辑**。

字段说明:
- `range` / `updated` / `next`:数据窗口、更新日、下次刷新文案。
- `highlights[]`:本期变化,`{grp, date, tag, txt, src}`。
  - `grp` ∈ `main`(主要竞争者动态) / `other`(其他竞争者/行业);**main 组多抓、内容更细(每家多条),other 组精简(只留最重要的几条)**。
  - `date`:该动态的**推出/发生时间**,短格式如 `"5/31"` / `"6/1"` / `"7月起"` / `"趋势"`;务必带,方便判断新旧。
  - `tag` ∈ `t-new`(NEW红) / `t-up`(趋势黄) / `t-info`(待核实蓝)。`src` 原始链接(尽量带)。每组内**按时间倒序,最新放最上面**。
  - **窗口**:`range` 维持**滚动近 2 周**;highlights 只保留近 2 周内的动态,**更早但重要的背景挪进对应 competitor 卡片**(卡片不受时间窗约束),别让旧条目堆在高亮里。每轮更新 `range` 末端到当天。
- `competitors[]`:逐家。`tier` ∈ `1`(主要竞品,卡片打金标) / `2`(其他,默认)。`status` ∈ `live`(绿) / `soon`(黄) / `watch`(灰)。
  - `promo[]` 宣发手段、`activities[]` 在跑活动:每一项可以是 **字符串** 或 **`{t:"文本", src:"链接"}`**(有原始出处就用对象带 src)。
  - `xsig` X 信号(远程抓不到登录态的标 🔒)。
  - `sources[]`:卡片底部信源 chips `[{label, url}]`(官方活动页/关键新闻/官推)。
- `techniques[]`:宣发手段矩阵,列要和 `competitors` 对齐(键名:bitget/binance/okx/gate/bybit/htx/kraken/coinbase…),每家取值 `1`(在用) / `0`(未用) / `"?"`(待抓取)。

> 注:已移除 `align`(BIT 可对齐)和 `actions`(Playbook)——这些交给市场部,看板只客观呈现竞品动态。

### 主要竞争者分级(重要)
当前判定 **主要竞争者 = Bitget、Binance**(`tier:1`,highlights 用 `grp:"main"`)——理由:中文向 + 正面冲真券商/美股 + 拉新最凶 + 与 BIT 打法重叠最深。其余为 `tier:2` / `grp:"other"`。
**每轮复核这个分级**:若某家(如 OKX/Bybit)动作升级到直接威胁 BIT 中文用户的程度,把它升为 tier:1 并把其动态归入 main 组;反之降级。判定标准:①是否中文向抢同一批用户 ②是否做真券商/美股且拉新激进 ③规模/势能威胁。

### 新入场者监控(重要)
每轮顺带扫一下市场上**是否有新的交易所/平台加入美股/券商/代币化股票这条赛道**(如 KuCoin、MEXC、Crypto.com、Robinhood、Gemini 等)。一旦发现有实质动作,**在 `competitors[]` 新增一张卡片 + 在 `techniques[]` 每行补一个对应键**,并在 highlights 里报「新入场者」。

### 链接纪律(重要)
**每条写进来的内容尽量带原始信源链接**(highlights.src / activities 项的 src / competitors.sources / actions.url),方便人工点开核对原文。官方活动页 > 官推原帖 > 权威新闻 > KOL。拿不到可靠链接就先不带 src,但别编链接。

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
- **分清美股产品形态,别只看 X 顶部几条就下结论**:每家「美股」常是多产品 hub,要翻官站「美股」下拉/产品页核对。四分类:① 真股 CFD(如 Gate TradFi,MT5 接真实价格)② 代币化(xStocks / Ondo,1:1 真股托管)③ 盘前交易 ④ 事件合约/预测市场。例:Gate = TradFi 真股CFD + Ondo 代币化 + 盘前 + Polymarket Live,**不是只有 xStocks**。
