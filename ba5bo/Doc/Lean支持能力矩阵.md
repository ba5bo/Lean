# Lean 引擎支持能力矩阵

> 生成日期: 2026-06-02 | 基于 QuantConnect Lean 核心代码库分析

---

## 目录

- [1. 中国内地市场支持总览](#1-中国内地市场支持总览)
- [2. 交易所与市场](#2-交易所与市场)
- [3. 经纪商模型](#3-经纪商模型)
- [4. 期货合约详情](#4-期货合约详情)
- [5. 数据源](#5-数据源)
- [6. 资产类型 (SecurityType)](#6-资产类型-securitytype)
- [7. 基础设施与可扩展性](#7-基础设施与可扩展性)
- [8. 添加中国期货支持的实施路径](#8-添加中国期货支持的实施路径)

---

## 1. 中国内地市场支持总览

### 结论: 当前不支持任何中国内地交易所、期货合约或交易平台

| 维度 | 状态 | 说明 |
|------|------|------|
| 中国内地期货交易所 | **不支持** | SHFE / DCE / CZCE / CFFEX / INE 均无定义 |
| 中国内地股票交易所 | **不支持** | SSE / SZSE 均无定义 |
| 中国内地交易/数据平台 | **不支持** | CTP / 恒生 / 易盛 / 飞马 / 金仕达 均无实现 |
| 中国内地数据源 | **不支持** | 无 Wind / Choice / Tushare 等集成 |
| 中国相关间接品种 | **支持** | CME 上市的 FTSE China 50、USD/CNH 期货 |
| 基础设施预留 | **可用** | 时区、货币、国家代码、动态 Market.Add() 均已就绪 |

---

## 2. 交易所与市场

### 2.1 中国内地交易所 (全部不支持)

| 交易所 | 代码 | 类型 | 状态 | 说明 |
|--------|------|------|------|------|
| 上海期货交易所 | SHFE | 商品期货 | **不支持** | `Market.cs` 中无市场常量，无合约定义，无交易时间 |
| 大连商品交易所 | DCE | 商品期货 | **不支持** | 同上 |
| 郑州商品交易所 | CZCE | 商品期货 | **不支持** | 同上 |
| 中国金融期货交易所 | CFFEX | 金融期货 | **不支持** | 同上 |
| 上海国际能源交易中心 | INE | 能源期货 | **不支持** | 同上 |
| 上海证券交易所 | SSE | 股票/指数 | **不支持** | 无市场常量，无股票定义 |
| 深圳证券交易所 | SZSE | 股票/指数 | **不支持** | 同上 |

### 2.2 已支持的期货交易所 (12 个)

| 交易所 | Market ID | 地区 | 主要品种类别 | 市场时间条目数 |
|--------|-----------|------|-------------|---------------|
| CME (芝加哥商业交易所) | `cme` | 美国 | 股指、外汇、畜产、林业 | ~75 |
| CBOT (芝加哥期货交易所) | `cbot` | 美国 | 谷物、国债、股指、能源 | ~25 |
| NYMEX (纽约商业交易所) | `nymex` | 美国 | 能源、金属 | ~82 |
| COMEX (商品交易所) | `comex` | 美国 | 贵金属 (金、银、铜) | ~9 |
| ICE (洲际交易所) | `ice` | 美国/英国 | 外汇、软商品、能源 | ~9 |
| CFE (CBOE 期货交易所) | `cfe` | 美国 | VIX 波动率指数 | ~3 |
| CME Globex | `cmeglobex` | 美国 | 电子交易 | — |
| EUREX | `eurex` | 欧洲 | DAX、Euro STOXX 50 | ~3 |
| SGX (新加坡交易所) | `sgx` | 亚洲 | 日经225、MSCI台湾、Nifty | ~4 |
| HKFE (香港期货交易所) | `hkfe` | 亚洲 | 恒生指数 | ~4 |
| OSE (大阪证券交易所) | `ose` | 亚洲 | 股指 | ~2 |
| NYSELIFFE | `nyseliffe` | 欧洲 | MSCI 指数 | ~1 |

### 2.3 已支持的股票/指数市场 (3 个)

| 市场 | Market ID | 地区 | 状态 |
|------|-----------|------|------|
| USA (NYSE / NASDAQ 等) | `usa` | 北美 | 支持 |
| India (NSE) | `india` | 亚洲 | 支持 |
| CBOE (指数) | `cboe` | 美国 | 支持 |

### 2.4 已支持的外汇/差价合约市场 (4 个)

| 市场 | Market ID | 类型 | 状态 |
|------|-----------|------|------|
| OANDA | `oanda` | 外汇 / CFD | 支持 |
| FXCM | `fxcm` | 外汇 / CFD | 支持 |
| Interactive Brokers | `interactivebrokers` | CFD | 支持 |
| Dukascopy | `dukascopy` | 数据提供商 | 部分 |

### 2.5 已支持的加密货币交易所 (16 个)

| 交易所 | Market ID | 状态 |
|--------|-----------|------|
| Binance | `binance` | 支持 |
| Binance US | `binanceus` | 支持 |
| Coinbase | `coinbase` | 支持 |
| Kraken | `kraken` | 支持 |
| Bitfinex | `bitfinex` | 支持 |
| Bybit | `bybit` | 支持 |
| dYdX | `dydx` | 支持 |
| Bitstamp | `bitstamp` | 支持 |
| Poloniex | `poloniex` | 支持 |
| Bithumb | `bithumb` | 支持 |
| Coinone | `coinone` | 支持 |
| HitBTC | `hitbtc` | 支持 |
| OkCoin | `okcoin` | 支持 |
| Bittrex | `bittrex` | 支持 |
| FTX | `ftx` | 已废弃 |
| FTX US | `ftxus` | 已废弃 |

---

## 3. 经纪商模型

### 3.1 中国内地交易/数据平台 (全部不支持)

| 平台 | 类型 | 状态 | 说明 |
|------|------|------|------|
| CTP (综合交易平台) | 期货交易与数据 | **不支持** | 无 IBrokerage 实现，无 IDataQueueHandler |
| 恒生 (Hundsun) | 经纪/交易 | **不支持** | 代码中无任何引用 |
| 易盛 (Esunny) | 期货交易 | **不支持** | 代码中无任何引用 |
| 飞马 (Feima) | 期货交易 | **不支持** | 代码中无任何引用 |
| 金仕达 (Kingstar) | 经纪/交易 | **不支持** | 代码中无任何引用 |
| 同花顺 / 东方财富 | 股票交易 | **不支持** | 代码中无任何引用 |

### 3.2 已支持的经纪商模型矩阵 (33 个)

> 注: 实际的 IBrokerage + IDataQueueHandler 实现在外部插件仓库中。核心仓库包含用于回测/模拟的经纪商模型。

#### 支持期货交易的经纪商 (10 个)

| 经纪商模型 | Equity | Option | Future | FutOpt | IdxOpt | Index | Forex | CFD | Crypto | CryptoFut |
|-----------|--------|--------|--------|--------|--------|-------|-------|-----|--------|-----------|
| **Default** | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |
| **Interactive Brokers** | Y | Y | Y | Y | Y | Y | Y | Y | - | - |
| **Interactive Brokers (FIX)** | Y | Y | Y | Y | Y | Y | Y | Y | - | - |
| **TradeStation** | Y | Y | Y | - | Y | - | - | - | - | - |
| **Tastytrade** | Y | Y | Y | Y | Y | - | - | - | - | - |
| **Trading Technologies** | - | - | Y | - | - | - | - | - | - | - |
| **Exante** | Y | Y | Y | - | - | Y | Y | Y | Y | - |
| **Eze** | Y | Y | Y | Y | Y | Y | - | - | - | - |
| **Samco** (印度) | Y | Y | Y | - | - | - | - | - | - | - |
| **AlphaStreams** | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y |

#### 仅支持加密期货的经纪商 (4 个)

| 经纪商模型 | Crypto | CryptoFut | 说明 |
|-----------|--------|-----------|------|
| **Binance Futures** | Y | Y | USD-M 加密期货 |
| **Binance Coin Futures** | Y | Y | COIN-M 加密期货 |
| **Bybit** | Y | Y | 加密永续合约 |
| **dYdX** | - | Y | 加密永续合约 |

#### 不支持期货的经纪商 (19 个)

| 经纪商模型 | 支持的资产类型 | 主要市场 |
|-----------|--------------|---------|
| **Alpaca** | Equity, Option, Crypto | USA |
| **Tradier** | Equity, Option, IndexOption | USA |
| **Charles Schwab** | Equity, Option, IndexOption | USA |
| **Webull** | Equity, Option, IndexOption | USA |
| **Wolverine** | Equity, Option | USA |
| **Binance** | Crypto, CryptoFuture | Binance |
| **Binance US** | Crypto, CryptoFuture (1x) | BinanceUS |
| **Coinbase** | Crypto | Coinbase |
| **Kraken** | Crypto | Kraken |
| **Bitfinex** | Crypto | Bitfinex |
| **OANDA** | Forex, CFD | Oanda |
| **FXCM** | Forex, CFD | FXCM |
| **Zerodha** (印度) | Equity | India |
| **Axos Clearing** | Equity | USA |
| **TD Ameritrade** | Equity | USA |
| **RBI** | Equity | USA |
| **FTX** | Crypto | FTX |
| **FTX US** | Crypto | FTXUS |
| **GDAX** (已废弃) | Crypto | Coinbase |

### 3.3 经纪商默认市场分配

| 经纪商 | 股票市场 | 加密市场 | 外汇市场 | 期货市场 |
|--------|---------|---------|---------|---------|
| Default | USA | Coinbase | Oanda | CME |
| Interactive Brokers | USA | - | Oanda | CME |
| TradeStation | USA | Coinbase | Oanda | CME |
| Tastytrade | USA | Coinbase | Oanda | CME |
| Trading Technologies | - | - | - | CME |
| Samco | **India** | Coinbase | Oanda | **India** |
| Zerodha | **India** | Coinbase | Oanda | CME |
| Binance | USA | **Binance** | Oanda | CME |
| Bybit | USA | **Bybit** | Oanda | **Bybit** |
| dYdX | USA | Coinbase | Oanda | **DYDX** |
| OANDA | USA | - | **Oanda** | - |
| FXCM | USA | - | **FXCM** | - |

---

## 4. 期货合约详情

### 4.1 中国内地期货合约 (全部不支持)

| 交易所 | 典型合约 | 状态 | 说明 |
|--------|---------|------|------|
| **SHFE** | CU (铜), AL (铝), RB (螺纹钢), RU (橡胶), AU (黄金), AG (白银), FU (燃油) 等 | **不支持** | `Futures.cs` 中无合约定义 |
| **DCE** | C (玉米), CS (淀粉), A (豆一), M (豆粕), Y (豆油), I (铁矿石), JD (鸡蛋), L (塑料), PP (聚丙烯) 等 | **不支持** | 同上 |
| **CZCE** | CF (棉花), SR (白糖), TA (PTA), OI (菜油), RM (菜粕), MA (甲醇), FG (玻璃) 等 | **不支持** | 同上 |
| **CFFEX** | IF (沪深300), IH (上证50), IC (中证500), IM (中证1000), T (10年国债), TF (5年国债), TS (2年国债), IO/MO (期权) | **不支持** | 同上 |
| **INE** | SC (原油), NR (20号胶), LU (低硫燃油), BC (国际铜) | **不支持** | 同上 |

### 4.2 已支持的期货品种类别

| 类别 | 交易所 | 合约数量 (约) | 代表品种 |
|------|--------|-------------|---------|
| **股指** | CME, CBOT, CFE, EUREX, SGX, HKFE, OSE, NYSELIFFE | ~35 | ES (标普500), NQ (纳指100), YM (道指), VX (VIX), FESX (欧洲STOXX50), HSI (恒生) |
| **能源** | NYMEX, ICE, CBOT | ~65 | CL (WTI原油), NG (天然气), RB (汽油), HO (取暖油), B (布伦特原油) |
| **金属** | COMEX, NYMEX | ~12 | GC (黄金), SI (白银), HG (铜), PL (铂金), PA (钯金) |
| **谷物** | CBOT | ~11 | ZC (玉米), ZS (大豆), ZW (小麦), ZM (豆粕), ZL (豆油) |
| **外汇** | CME, ICE | ~40 | 6E (欧元), 6B (英镑), 6J (日元), DX (美元指数), CNH (离岸人民币) |
| **国债** | CBOT, CME | ~15 | ZB (30年), ZN (10年), ZF (5年), ZT (2年) |
| **软商品** | ICE, NYMEX | ~6 | CT (棉花), KC (咖啡), SB (糖), CC (可可), OJ (橙汁) |
| **畜产** | CME | ~3 | LE (活牛), GF (架子牛), HE (瘦猪) |
| **林业** | CME | ~2 | LBS (木材) |

### 4.3 中国相关期货 (在非中国交易所上市)

| 合约名称 | 代码 | 交易所 | 状态 |
|---------|------|--------|------|
| E-Mini FTSE China 50 Index | FT5 | CME | 支持 |
| Standard-Size USD/Offshore RMB | CNH | CME | 支持 |
| Micro USD/CNH | MNH | CME | 支持 |

---

## 5. 数据源

### 5.1 中国内地数据源 (全部不支持)

| 数据源 | 类型 | 状态 | 说明 |
|--------|------|------|------|
| CTP 行情数据 | 实时期货行情 | **不支持** | 无 IDataQueueHandler 实现 |
| SHFE / DCE / CZCE / CFFEX 交易所直连 | 交易所数据 | **不支持** | 无任何集成 |
| Wind (万得) | 金融数据终端 | **不支持** | 无集成 |
| Choice (东方财富) | 金融数据终端 | **不支持** | 无集成 |
| Tushare | 开源金融数据 | **不支持** | 无集成 |
| SSE / SZSE 行情 | 股票实时数据 | **不支持** | 无集成 |
| 中国A股基本面数据 | 基本面 | **不支持** | 无集成 |

### 5.2 已支持的数据源

| 数据源 | 类型 | 覆盖市场 | 状态 |
|--------|------|---------|------|
| **Lean 本地数据文件** | 价格 (OHLCV / Tick / Quote) | 所有已支持市场 | 支持 |
| **经纪商历史数据** (IBrokerage.GetHistory) | 历史价格 | 取决于经纪商 | 支持 |
| **Intrinio (FRED)** | 宏观经济指标 | 美国宏观、全球汇率、大宗商品 | 部分 |
| **Tiingo API** | 日线股票价格 | 美国股票 | 支持 |
| **FXCM 成交量** | 外汇成交量/交易数据 | 14 个货币对 | 支持 |
| **AlgoSeek** | 期货 (Tick/成交/报价) | 美国期货 | 支持 |
| **Kaiko** | 加密货币 (Tick/订单簿) | 加密交易所 | 支持 |
| **随机数据生成器** | 合成测试数据 | 任意配置市场 | 支持 |

### 5.3 Intrinio 中国相关经济指标 (间接)

| 指标名称 | 代码 | 状态 |
|---------|------|------|
| CBOE China ETF Volatility Index | `$VXFXICLS` | 间接 |
| Chinese Yuan to One US Dollar | `$DEXCHUS` | 间接 |
| Trade-Weighted USD (Broad, 含中国) | `$DTWEXB` | 间接 |
| Trade-Weighted USD (Other Partners, 含中国) | `$DTWEXO` | 间接 |

### 5.4 工具链数据转换器

| 转换器 | 数据类型 | 说明 |
|--------|---------|------|
| AlgoSeek Futures Converter | 期货 Tick/成交/报价 | 将 AlgoSeek 原始数据转为 Lean 格式 |
| Kaiko Crypto Converter | 加密货币 Tick/订单簿 | 将 Kaiko 原始数据转为 Lean 格式 |
| Coarse Universe Generator | 股票粗选数据 | 从日线数据生成粗选宇宙文件 |
| Random Data Generator | 合成数据 | 生成 Equity / Option / Future / Forex / Crypto 随机数据 |

---

## 6. 资产类型 (SecurityType)

| SecurityType | 说明 | 默认市场 | 中国内地支持 | 全局状态 |
|-------------|------|---------|-------------|---------|
| **Equity** | 股票 | USA | **不支持** — 无 SSE/SZSE | 支持 |
| **Option** | 股票期权 | USA | **不支持** — 无中国期权 | 支持 |
| **Future** | 期货合约 | CME | **不支持** — 无 SHFE/DCE/CZCE/CFFEX/INE | 支持 |
| **FutureOption** | 期货期权 | CME | **不支持** — 无中国期货期权 | 支持 |
| **Index** | 市场指数 | USA | **不支持** — 无 CSI 300/SSE 50 等 | 支持 |
| **IndexOption** | 指数期权 | USA | **不支持** — 无中国指数期权 | 支持 |
| **Forex** | 外汇 | Oanda | **部分** — CNH 可通过 OANDA/IB/FXCM 交易 | 支持 |
| **Cfd** | 差价合约 | Oanda | **不支持** | 支持 |
| **Crypto** | 加密货币 | Coinbase | N/A (去中心化) | 支持 |
| **CryptoFuture** | 加密期货 | Binance | N/A (去中心化) | 支持 |
| **Commodity** | 实物商品 | N/A | **不支持** | 部分 |

---

## 7. 基础设施与可扩展性

### 7.1 已就绪的中国市场基础设施

| 组件 | 文件位置 | 状态 | 说明 |
|------|---------|------|------|
| `Market.Add()` 动态注册 | `Common/Market.cs` | 可用 | 可在运行时注册 SHFE/DCE/CZCE/CFFEX/INE (ID < 1000) |
| `TimeZones.Shanghai` | `Common/TimeZones.cs` | 可用 | `Asia/Shanghai` 时区已预定义 |
| `TimeZones.HongKong` | `Common/TimeZones.cs` | 可用 | `Asia/Hong_Kong` 时区已预定义 |
| `Currencies.CNH` / `CNY` | `Common/Currencies.cs` | 可用 | CNH (离岸) + CNY (在岸) 符号已定义 |
| `Country.China` | `Common/Country.cs` | 可用 | ISO 代码 "CHN" 已定义 |
| `Futures.cs` 可扩展结构 | `Common/Securities/Future/Futures.cs` | 可用 | 可添加中国期货品种分类 |
| `FuturesExpiryFunctions` 字典 | `Common/Securities/Future/FuturesExpiryFunctions.cs` | 可用 | 可添加中国合约到期规则 |
| `IBrokerage` 接口 | `Common/Interfaces/IBrokerage.cs` | 可用 | 可实现 CTP 经纪商适配器 |
| `IDataQueueHandler` 接口 | `Common/Interfaces/IDataQueueHandler.cs` | 可用 | 可实现 CTP 行情数据处理器 |
| `IBrokerageFactory` (MEF) | `Common/Interfaces/IBrokerageFactory.cs` | 可用 | MEF 插件发现机制 |
| 市场时间数据库 | `Data/market-hours/market-hours-database.json` | 可用 | 可添加中国交易所交易时间 |
| 合约属性数据库 | `Data/symbol-properties/symbol-properties-database.csv` | 可用 | 可添加中国合约属性 (保证金、最小变动价位等) |

### 7.2 缺失的中国市场组件

| 组件 | 文件位置 | 状态 | 说明 |
|------|---------|------|------|
| 市场时间 — 中国条目 | `Data/market-hours/market-hours-database.json` | **缺失** | SHFE/DCE/CZCE/CFFEX/INE 条目数为 0 |
| 合约属性 — 中国条目 | `Data/symbol-properties/symbol-properties-database.csv` | **缺失** | 中国交易所合约条目数为 0 |
| 市场常量 — 中国交易所 | `Common/Market.cs` | **缺失** | 无 SHFE/DCE/CZCE/CFFEX/INE/SSE/SZSE |
| 期货合约定义 — 中国 | `Common/Securities/Future/Futures.cs` | **缺失** | 无中国期货合约符号定义 |
| 经纪商模型 — 中国 | `Common/Brokerages/` | **缺失** | 无 CTPBrokerageModel 或任何中国经纪商模型 |

---

## 8. 添加中国期货支持的实施路径

> 以下为实现中国内地期货交易的完整技术路线:

### 步骤 1: 市场常量注册

在 `Common/Market.cs` 中通过 `Market.Add()` 注册中国交易所:

```csharp
Market.Add("SHFE", 100);  // 上海期货交易所
Market.Add("DCE",  101);  // 大连商品交易所
Market.Add("CZCE", 102);  // 郑州商品交易所
Market.Add("CFFEX", 103); // 中国金融期货交易所
Market.Add("INE",  104);  // 上海国际能源交易中心
```

### 步骤 2: 期货合约定义

在 `Common/Securities/Future/Futures.cs` 中添加中国期货品种分类和合约符号:

```csharp
public static class SHFE
{
    public const string Copper = "CU";
    public const string Aluminum = "AL";
    public const string Rebar = "RB";
    public const string Gold = "AU";
    public const string Silver = "AG";
    // ...
}

public static class DCE
{
    public const string Corn = "C";
    public const string IronOre = "I";
    public const string SoybeanMeal = "M";
    // ...
}

public static class CFFEX
{
    public const string CSI300Index = "IF";
    public const string SSE50Index = "IH";
    public const string CSI500Index = "IC";
    // ...
}
```

### 步骤 3: 合约到期函数

在 `Common/Securities/Future/FuturesExpiryFunctions.cs` 中为每个中国合约添加到期规则。

### 步骤 4: 交易时间配置

在 `Data/market-hours/market-hours-database.json` 中添加中国交易所交易时间:

- **夜盘**: 21:00 - 02:30 (次日)
- **日盘**: 09:00 - 10:15, 10:30 - 11:30, 13:30 - 15:00
- **CFFEX 股指**: 09:30 - 11:30, 13:00 - 15:00
- **CFFEX 国债**: 09:30 - 11:30, 13:00 - 15:15

### 步骤 5: 合约属性配置

在 `Data/symbol-properties/symbol-properties-database.csv` 中添加合约属性:

- 保证金要求
- 最小变动价位 (tick size)
- 合约乘数
- 结算货币 (CNY)

### 步骤 6: 经纪商模型实现

实现 `CTPBrokerageModel`:

- `CTPFeeModel` — 手续费模型 (按手/按金额)
- `CTPMarginModel` — 保证金模型
- `CTPFillModel` — 成交模型
- `CTPSlippageModel` — 滑点模型
- `CTPSettlementModel` — 结算模型

### 步骤 7: CTP 交易接口实现

实现 `CTPBrokerage` (IBrokerage):

- 通过 CTP API 进行委托下单/撤单
- 持仓查询、资金查询
- 成交回报处理
- 需要 P/Invoke 封装 CTP C++ API 或使用 .NET CTP 封装库

### 步骤 8: CTP 行情数据实现

实现 `CTPDataQueueHandler` (IDataQueueHandler):

- 订阅/取消订阅 CTP 行情
- 实时行情推送 (Tick / K线)
- 行情数据转 Lean 格式

### 步骤 9: 插件工厂

实现 `CTPBrokerageFactory` (IBrokerageFactory):

- MEF 导出标记
- 配置参数 (BrokerID, UserID, Password, FrontAddr)
- 创建经纪商实例

### 步骤 10: CTP API 封装

- 通过 P/Invoke 封装 CTP C++ API (`thosttraderapi.so` / `thostmduserapi.so`)
- 或使用已有的 .NET CTP 封装库
- 处理跨平台兼容性 (Windows / Linux)

---

## 附录: 完整 BrokerageModel 列表

| # | BrokerageModel | 支持期货 | 主要市场 |
|---|---------------|---------|---------|
| 1 | DefaultBrokerageModel | Y | 全部 |
| 2 | InteractiveBrokersBrokerageModel | Y | USA / 全球 |
| 3 | InteractiveBrokersFixModel | Y | USA / 全球 |
| 4 | AlpacaBrokerageModel | - | USA |
| 5 | TradierBrokerageModel | - | USA |
| 6 | TradeStationBrokerageModel | Y | USA |
| 7 | BinanceBrokerageModel | - | Binance |
| 8 | BinanceFuturesBrokerageModel | Crypto | Binance |
| 9 | BinanceCoinFuturesBrokerageModel | Crypto | Binance |
| 10 | BinanceUSBrokerageModel | - | BinanceUS |
| 11 | BitfinexBrokerageModel | - | Bitfinex |
| 12 | CoinbaseBrokerageModel | - | Coinbase |
| 13 | GDAXBrokerageModel (已废弃) | - | Coinbase |
| 14 | FxcmBrokerageModel | - | FXCM |
| 15 | OandaBrokerageModel | - | OANDA |
| 16 | KrakenBrokerageModel | - | Kraken |
| 17 | BybitBrokerageModel | Crypto | Bybit |
| 18 | FTXBrokerageModel | - | FTX |
| 19 | FTXUSBrokerageModel | - | FTXUS |
| 20 | ExanteBrokerageModel | Y | 全球 |
| 21 | EzeBrokerageModel | Y | USA |
| 22 | SamcoBrokerageModel | Y | India |
| 23 | ZerodhaBrokerageModel | - | India |
| 24 | TradingTechnologiesBrokerageModel | Y | CME |
| 25 | AxosClearingBrokerageModel | - | USA |
| 26 | AlphaStreamsBrokerageModel | Y | 全部 |
| 27 | WolverineBrokerageModel | - | USA |
| 28 | TDAmeritradeBrokerageModel | - | USA |
| 29 | RBIBrokerageModel | - | USA |
| 30 | CharlesSchwabBrokerageModel | - | USA |
| 31 | TastytradeBrokerageModel | Y | USA |
| 32 | dYdXBrokerageModel | Crypto | dYdX |
| 33 | WebullBrokerageModel | - | USA |
