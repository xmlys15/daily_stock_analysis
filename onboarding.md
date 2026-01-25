# 📘 新人上手指南 (Onboarding Guide)

## 📌 项目概览

**一句话用途**：基于 AI 大模型的 A/H 股自选股智能分析系统，每日自动分析并推送「决策仪表盘」到企业微信/飞书/Telegram/邮箱。

**核心流程**：

```
定时触发 (GitHub Actions/本地调度)
    ↓
拉取数据 (5 数据源自动切换)
    ↓
技术分析 (MA趋势 + 筹码分布 + 实时行情)
    ↓
新闻搜索 (多维度：新闻 + 风险 + 业绩)
    ↓
AI 分析 (Gemini/OpenAI 兼容 API)
    ↓
生成报告 (决策仪表盘 + 大盘复盘)
    ↓
多渠道推送 (企业微信/飞书/Telegram/邮件等)
```

---

## ✅ 你需要准备什么

### 1. Python 环境
- **Python 版本**：3.10+ (推荐 3.11 或 3.12)
- **操作系统**：Windows/Linux/macOS 均支持

### 2. 依赖安装方式

**查看依赖清单**：[requirements.txt](requirements.txt)

**核心依赖**：
- `google-generativeai` - Gemini API (主力 AI 模型)
- `openai` - OpenAI 兼容 API (备选)
- `akshare`, `tushare`, `baostock`, `efinance`, `yfinance` - 5 个数据源 (自动切换)
- `tavily-python`, `google-search-results` - 新闻搜索
- `sqlalchemy` - SQLite 数据库
- `schedule` - 定时任务
- `pandas`, `numpy` - 数据处理

**安装命令**：
```bash
pip install -r requirements.txt
```

### 3. 外部账号/Token/密钥 (必需)

#### A. AI 模型配置 (二选一，至少配置一个)

**方案一：Google Gemini (推荐，有免费额度)**
- **获取方式**：访问 [Google AI Studio](https://aistudio.google.com/)，免费注册并创建 API Key
- **配置项**：
  - `GEMINI_API_KEY` - 你的 Gemini API Key (必填)
  - `GEMINI_MODEL` - 模型名称 (默认：`gemini-3-flash-preview`)
  - `GEMINI_MODEL_FALLBACK` - 备用模型 (默认：`gemini-2.5-flash`)

**方案二：OpenAI 兼容 API (支持 DeepSeek/通义千问/Moonshot 等)**
- **配置项**：
  - `OPENAI_API_KEY` - 你的 API Key (必填)
  - `OPENAI_BASE_URL` - API 地址 (可选，例如 DeepSeek：`https://api.deepseek.com/v1`)
  - `OPENAI_MODEL` - 模型名称 (可选，例如：`deepseek-chat`)

#### B. 通知渠道配置 (至少配置一个，可同时配置多个)

**企业微信**：
- `WECHAT_WEBHOOK_URL` - 企业微信群机器人 Webhook URL
- 获取方式：企业微信群 → 设置 → 群机器人 → 添加 → 复制 Webhook 地址

**飞书**：
- `FEISHU_WEBHOOK_URL` - 飞书群机器人 Webhook URL
- 获取方式：飞书群 → 设置 → 群机器人 → 添加机器人 → 自定义机器人 → 复制 Webhook 地址

**Telegram**：
- `TELEGRAM_BOT_TOKEN` - Bot Token
- `TELEGRAM_CHAT_ID` - Chat ID
- 获取方式：
  1. 在 Telegram 找 @BotFather → 发送 `/newbot` → 获取 Bot Token
  2. 发消息给 @userinfobot 或访问 `https://api.telegram.org/bot<token>/getUpdates` 获取 Chat ID

**邮件**：
- `EMAIL_SENDER` - 发件人邮箱 (如 `xxx@qq.com`)
- `EMAIL_PASSWORD` - 邮箱授权码 (非登录密码)
- `EMAIL_RECEIVERS` - 收件人邮箱 (可选，多个用逗号分隔，留空则发给自己)
- 支持自动识别 SMTP：QQ邮箱/163邮箱/Gmail/Outlook 等
- QQ 邮箱授权码获取：设置 → 账户 → POP3/SMTP服务 → 开启 → 获取授权码

**其他渠道**：
- `CUSTOM_WEBHOOK_URLS` - 自定义 Webhook (支持钉钉/Slack/Bark 等，多个用逗号分隔)
- `PUSHOVER_USER_KEY` + `PUSHOVER_API_TOKEN` - Pushover 手机推送

#### C. 新闻搜索配置 (可选，但强烈推荐)

- `TAVILY_API_KEYS` - [Tavily](https://tavily.com/) 搜索 API (多个 key 用逗号分隔，每月 1000 次免费)
- `BOCHA_API_KEYS` - [博查搜索](https://open.bocha.cn/) API (多个 key 用逗号分隔，中文搜索优化)
- `SERPAPI_API_KEYS` - [SerpAPI](https://serpapi.com/) 备用搜索 (多个 key 用逗号分隔，每月 100 次免费)

#### D. 数据源配置 (可选)

- `TUSHARE_TOKEN` - [Tushare Pro](https://tushare.pro/) Token (可选，免费注册即可)
- 其他数据源 (AkShare/Baostock/eFINANCE/YFinance) 均为免费，无需配置

#### E. 自选股列表 (必需)

- `STOCK_LIST` - 股票代码，逗号分隔 (例如：`600519,300750,002594`)
- 支持沪深两市：
  - 沪市：`600xxx`, `601xxx`, `603xxx`
  - 深市：`000xxx`, `002xxx`, `300xxx`

---

## 🚀 快速开始 (本地一次性运行)

### 1. 安装依赖

```bash
# 克隆仓库
git clone <your-repo-url>
cd daily_stock_analysis

# 安装 Python 依赖
pip install -r requirements.txt
```

### 2. 配置环境变量

**方式一：使用 .env 文件 (推荐)**

```bash
# 复制配置模板
cp .env.example .env

# 编辑配置文件，填入真实配置
vim .env  # 或使用其他编辑器
```

**必要配置项说明**：

参考 `.env.example` 文件，逐项填写：

- **AI 模型** (`GEMINI_API_KEY` 或 `OPENAI_API_KEY` 至少一个)
- **通知渠道** (至少配置一个)
- **自选股列表** (`STOCK_LIST`)

**可选配置项**：

- `TAVILY_API_KEYS` / `BOCHA_API_KEYS` / `SERPAPI_API_KEYS` - 新闻搜索 (强烈推荐)
- `TUSHARE_TOKEN` - Tushare 数据源
- `SCHEDULE_ENABLED` - 是否启用定时任务 (默认 `false`)
- `SCHEDULE_TIME` - 每日执行时间 (默认 `18:00`)
- `MARKET_REVIEW_ENABLED` - 是否启用大盘复盘 (默认 `true`)
- `MAX_WORKERS` - 最大并发线程数 (默认 `3`)
- `LOG_LEVEL` - 日志级别 (默认 `INFO`)

**方式二：直接设置环境变量**

```bash
export GEMINI_API_KEY="your_gemini_api_key"
export STOCK_LIST="600519,300750"
export WECHAT_WEBHOOK_URL="https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx"
```

### 3. 一次性运行命令

**完整分析 (股票 + 大盘)**：
```bash
python main.py
```

**仅股票分析 (不含大盘)**：
```bash
python main.py --no-market-review
```

**仅大盘复盘**：
```bash
python main.py --market-review
```

**调试模式 (显示详细日志)**：
```bash
python main.py --debug
```

**干跑模式 (仅获取数据，不调用 AI)**：
```bash
python main.py --dry-run
```

**其他常用参数**：
- `--stocks CODE1,CODE2` - 临时指定股票列表 (覆盖配置)
- `--no-notify` - 跳过推送通知
- `--single-notify` - 每分析完一只股票立即推送 (而非批量推送)
- `--workers N` - 并发线程数 (默认 3)

### 4. 如何验证成功

**查看控制台日志**：
```
2026-01-20 18:00:00 | INFO     | __main__             | 日志系统初始化完成，日志目录: /path/to/logs
2026-01-20 18:00:05 | INFO     | __main__             | 调度器初始化完成，最大并发数: 3
2026-01-20 18:00:10 | INFO     | __main__             | [600519] 开始分析...
2026-01-20 18:00:30 | INFO     | analyzer             | [600519] AI 分析完成
2026-01-20 18:00:35 | INFO     | notification         | 消息推送成功 (企业微信)
```

**查看生成的文件**：
- `data/stock_analysis.db` - SQLite 数据库 (存储历史数据)
- `logs/stock_analysis_YYYYMMDD.log` - 常规日志
- `logs/stock_analysis_debug_YYYYMMDD.log` - 调试日志
- `reports/` - 分析报告 (如有启用)

**查看推送通知**：
- 检查配置的通知渠道 (企业微信/飞书/Telegram/邮箱) 是否收到消息
- 消息格式：
  ```
  📊 2026-01-20 决策仪表盘
  3只股票 | 🟢买入:1 🟡观望:2 🔴卖出:0

  🟢 买入 | 贵州茅台(600519)
  📌 缩量回踩MA5支撑，乖离率1.2%处于最佳买点
  💰 狙击: 买入1800 | 止损1750 | 目标1900
  ✅多头排列 ✅乖离安全 ✅量能配合
  ```

---

## ⭐ 推荐方式：GitHub Actions 定时运行 (零成本)

### 1. Workflow 文件路径

`.github/workflows/daily_analysis.yml`

### 2. 触发方式

#### 自动触发 (定时 Cron)
- **时间**：每个工作日 **18:00 (北京时间)** / **10:00 (UTC)**
- **Cron 表达式**：`0 10 * * 1-5` (周一到周五)
- **配置位置**：`.github/workflows/daily_analysis.yml` 第 5-6 行

#### 手动触发 (workflow_dispatch)
- **位置**：GitHub 仓库 → `Actions` → `每日股票分析` → `Run workflow`
- **运行模式**：
  - `full` - 完整分析 (股票 + 大盘)
  - `market-only` - 仅大盘复盘
  - `stocks-only` - 仅股票分析

### 3. 需要配置的 Secrets

**配置位置**：你的 GitHub 仓库 → `Settings` → `Secrets and variables` → `Actions` → `New repository secret`

**必需 Secrets**：

| Secret 名称 | 用途 | 获取方式 |
|------------|------|---------|
| `GEMINI_API_KEY` | Gemini API (主力 AI 模型) | [Google AI Studio](https://aistudio.google.com/) |
| `STOCK_LIST` | 自选股代码 (逗号分隔) | 手动填写，例如 `600519,300750,002594` |

**至少一个通知渠道**：

| Secret 名称 | 用途 | 获取方式 |
|------------|------|---------|
| `WECHAT_WEBHOOK_URL` | 企业微信推送 | 企业微信群机器人 Webhook |
| `FEISHU_WEBHOOK_URL` | 飞书推送 | 飞书群机器人 Webhook |
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token | @BotFather |
| `TELEGRAM_CHAT_ID` | Telegram Chat ID | @userinfobot |
| `EMAIL_SENDER` | 发件人邮箱 | 你的邮箱地址 |
| `EMAIL_PASSWORD` | 邮箱授权码 | 邮箱设置中获取 |

**可选 Secrets (推荐配置)**：

| Secret 名称 | 用途 | 获取方式 |
|------------|------|---------|
| `TAVILY_API_KEYS` | 新闻搜索 (多个用逗号分隔) | [Tavily](https://tavily.com/) |
| `BOCHA_API_KEYS` | 中文搜索 (多个用逗号分隔) | [博查搜索](https://open.bocha.cn/) |
| `SERPAPI_API_KEYS` | 备用搜索 (多个用逗号分隔) | [SerpAPI](https://serpapi.com/) |
| `TUSHARE_TOKEN` | Tushare 数据源 | [Tushare Pro](https://tushare.pro/) |

**OpenAI 兼容 API (可选，作为 Gemini 备选)**：

| Secret 名称 | 用途 |
|------------|------|
| `OPENAI_API_KEY` | OpenAI 兼容 API Key |
| `OPENAI_BASE_URL` | API 地址 (如 DeepSeek：`https://api.deepseek.com/v1`) |
| `OPENAI_MODEL` | 模型名称 (如 `deepseek-chat`) |

**支持 Variables 配置 (非敏感信息，可放 Variables 而非 Secrets)**：

部分非敏感配置支持从 `Repository Variables` 读取 (位置：`Settings` → `Secrets and variables` → `Actions` → `Variables`)：
- `GEMINI_MODEL` - Gemini 模型名称
- `OPENAI_BASE_URL` - OpenAI API 地址
- `OPENAI_MODEL` - OpenAI 模型名称
- `STOCK_LIST` - 自选股列表
- `EMAIL_SENDER` - 发件人邮箱
- `EMAIL_RECEIVERS` - 收件人邮箱

### 4. 失败排查

#### 查看日志位置
1. GitHub 仓库 → `Actions` → 点击失败的运行
2. 点击 `analyze` Job → 展开 `执行股票分析` 步骤
3. 查看详细错误日志

#### 下载完整日志/报告
1. 运行完成后，点击 `Artifacts`
2. 下载 `analysis-reports-<run_number>` (包含完整日志和报告)

#### 常见失败原因

**API Key 无效或未配置**：
- 错误信息：`API key not found` / `Invalid API key`
- 解决方案：检查 Secrets 是否正确配置，注意 Secret 名称大小写

**股票代码格式错误**：
- 错误信息：`Invalid stock code`
- 解决方案：确保 `STOCK_LIST` 格式正确 (如 `600519,300750`)，无空格

**通知推送失败**：
- 错误信息：`Webhook URL not configured` / `Failed to send notification`
- 解决方案：至少配置一个通知渠道，检查 Webhook URL 是否有效

**时区问题**：
- 症状：定时触发时间不对
- 解决方案：Workflow 中的 Cron 使用 UTC 时间，北京时间需 -8 小时 (18:00 北京 = 10:00 UTC)

**数据源请求失败**：
- 错误信息：`Failed to fetch data from all sources`
- 解决方案：系统会自动切换 5 个数据源 (eFINANCE/AkShare/Tushare/Baostock/YFinance)，通常是临时网络问题，重试即可

**AI 分析超时**：
- 错误信息：`Timeout` / `Request timeout`
- 解决方案：GitHub Actions 环境建议增加 `GEMINI_REQUEST_DELAY` (已默认设为 3.0 秒)

---

## 📁 目录结构导览

### 顶层目录

```
daily_stock_analysis/
├── main.py                   # 【入口】主程序入口，调度器
├── config.py                 # 【核心】配置管理 (单例模式)
├── analyzer.py               # 【核心】AI 分析器 (Gemini/OpenAI 集成)
├── market_analyzer.py        # 【核心】大盘复盘分析
├── stock_analyzer.py         # 【核心】技术趋势分析 (MA5>MA10>MA20)
├── notification.py           # 【核心】多渠道通知服务
├── search_service.py         # 【核心】新闻搜索聚合 (Tavily/SerpAPI/Bocha)
├── storage.py                # 【核心】数据库管理 (SQLite + SQLAlchemy)
├── scheduler.py              # 【核心】定时任务调度
├── feishu_doc.py             # 【可选】飞书云文档集成
├── enums.py                  # 枚举类型定义
├── webui.py                  # 【可选】WebUI 入口 (向后兼容)
├── test_env.py               # 环境测试脚本
│
├── data_provider/            # 【数据层】多数据源适配器 (策略模式)
│   ├── __init__.py           # 数据源管理器导出
│   ├── base.py               # 抽象基类 + 管理器
│   ├── efinance_fetcher.py   # Priority 0: 东方财富 (eFINANCE)
│   ├── akshare_fetcher.py    # Priority 1: AkShare (含实时行情 + 筹码分布)
│   ├── tushare_fetcher.py    # Priority 2: Tushare Pro
│   ├── baostock_fetcher.py   # Priority 3: 证券宝
│   └── yfinance_fetcher.py   # Priority 4: Yahoo Finance (Fallback)
│
├── web/                      # 【WebUI 模块】本地管理界面
│   ├── __init__.py           # 包初始化
│   ├── server.py             # HTTP 服务器核心
│   ├── router.py             # URL 路由管理
│   ├── handlers.py           # 请求处理器
│   ├── services.py           # 业务逻辑服务
│   └── templates.py          # HTML 模板
│
├── .github/workflows/        # 【GitHub Actions】
│   ├── daily_analysis.yml    # 【主工作流】每日股票分析
│   ├── ci.yml                # 持续集成 (代码质量检查)
│   ├── pr-review.yml         # AI PR 审查
│   └── stale.yml             # Stale issue 管理
│
├── docs/                     # 文档目录
│   └── full-guide.md         # 完整配置指南
│
├── data/                     # 数据目录 (运行时生成)
│   └── stock_analysis.db     # SQLite 数据库
├── logs/                     # 日志目录 (运行时生成)
├── reports/                  # 报告目录 (运行时生成)
│
├── requirements.txt          # Python 依赖列表
├── .env.example              # 环境变量配置模板
├── Dockerfile                # Docker 镜像定义
├── docker-compose.yml        # Docker Compose 编排
├── README.md                 # 项目说明 (特性/快速开始)
├── DEPLOY.md                 # 部署指南 (Docker/直接部署/Systemd)
├── CONTRIBUTING.md           # 贡献指南
└── CHANGELOG.md              # 版本历史
```

### 核心模块文件说明

| 文件 | 职责 | 关键类/函数 |
|------|------|-----------|
| `main.py` | 主调度器、线程池管理、命令行入口 | `StockAnalysisPipeline`, `main()` |
| `analyzer.py` | AI 分析 (Gemini/OpenAI) | `GeminiAnalyzer`, `AnalysisResult` |
| `market_analyzer.py` | 大盘复盘 | `MarketAnalyzer` |
| `stock_analyzer.py` | 技术趋势分析 | `StockTrendAnalyzer`, `TrendAnalysisResult` |
| `notification.py` | 多渠道推送 | `NotificationService`, `send_daily_report()` |
| `search_service.py` | 新闻搜索聚合 | `SearchService`, `SearchResponse` |
| `storage.py` | 数据库 ORM | `DatabaseManager`, `get_db()` |
| `config.py` | 配置管理 (单例) | `Config`, `get_config()` |
| `data_provider/base.py` | 数据源基类 + 管理器 | `BaseFetcher`, `DataFetcherManager` |

---

## 🔍 主流程与关键模块

### 流程一：单只股票分析流程

**入口**：`main.py` → `StockAnalysisPipeline.analyze_single_stock()`

**调用链**：

1. **数据获取** (`main.py:172-224`)
   - `StockAnalysisPipeline.fetch_and_save_stock_data(code)`
   - → `DataFetcherManager.get_daily_data(code)` ([data_provider/base.py:193-245](data_provider/base.py))
   - → 按优先级尝试：eFINANCE → AkShare → Tushare → Baostock → YFinance
   - → `DatabaseManager.save_stock_data()` ([storage.py](storage.py))

2. **实时增强数据** (`main.py:226-243`)
   - `AkshareFetcher.get_realtime_quote(code)` ([data_provider/akshare_fetcher.py](data_provider/akshare_fetcher.py))
   - → 获取量比、换手率、市盈率等实时指标
   - `AkshareFetcher.get_chip_distribution(code)` ([data_provider/akshare_fetcher.py](data_provider/akshare_fetcher.py))
   - → 获取筹码分布、集中度

3. **趋势分析** (`main.py:245-257`)
   - `StockTrendAnalyzer.analyze(df)` ([stock_analyzer.py](stock_analyzer.py))
   - → 计算 MA5/MA10/MA20、多头排列、乖离率、买入信号评分

4. **新闻搜索** (`main.py:259-283`)
   - `SearchService.multi_dimensional_search(code, stock_name)` ([search_service.py](search_service.py))
   - → 并发搜索：最新新闻 + 风险调查 + 业绩预期
   - → 使用 Tavily/SerpAPI/Bocha 多引擎聚合

5. **AI 分析** (`main.py:285-303`)
   - `GeminiAnalyzer.analyze_stock(code, df, realtime, chip, trend, search)` ([analyzer.py](analyzer.py))
   - → 构建 Prompt (包含历史数据 + 实时行情 + 筹码 + 新闻 + 趋势 + 交易理念)
   - → 调用 Gemini API / OpenAI 兼容 API
   - → 返回 `AnalysisResult` (买入/观望/卖出 + 理由 + 价格点位)

6. **可选推送** (`main.py:305-312`)
   - 如启用 `--single-notify` 或配置 `SINGLE_STOCK_NOTIFY=true`
   - → `NotificationService.send_to_all_channels(message)` ([notification.py](notification.py))

### 流程二：完整每日分析流程

**入口**：`main.py` → `main()` → `run_daily_analysis()`

**调用链**：

1. **并发股票分析** (`main.py:615-677`)
   - 使用 `ThreadPoolExecutor` (默认 3 线程)
   - → 并发调用 `analyze_single_stock(code)` (流程一)
   - → 收集所有分析结果到 `List[AnalysisResult]`

2. **大盘复盘** (可选，默认启用) (`main.py:679-715`)
   - `MarketAnalyzer.analyze_market()` ([market_analyzer.py](market_analyzer.py))
   - → 获取主要指数 (上证/深证/创业板)
   - → 分析市场概况 (涨跌家数、涨停跌停)
   - → 板块表现、北向资金
   - → AI 总结市场趋势

3. **生成报告** (`main.py:717-742`)
   - `send_daily_report(results, market_analysis)` ([notification.py](notification.py))
   - → 汇总所有股票分析结果
   - → 生成决策仪表盘 (Markdown 格式)
   - → 统计买入/观望/卖出数量

4. **多渠道推送** (`main.py:744-760` + [notification.py](notification.py))
   - `NotificationService.send_to_all_channels(report)`
   - → 并发推送到所有已配置渠道：
     - 企业微信 Webhook
     - 飞书 Webhook
     - Telegram Bot API
     - 邮件 SMTP
     - Pushover API
     - 自定义 Webhook
   - → 自动处理消息长度限制 (超长自动分批)

5. **飞书云文档** (可选) (`main.py:762-778`)
   - `FeishuDocManager.create_and_upload_doc()` ([feishu_doc.py](feishu_doc.py))
   - → 上传完整报告到飞书云空间

---

## 📢 推送通道说明

### 支持的通道

系统支持同时配置多个通道，分析完成后会向所有已配置的渠道推送：

| 通道 | 配置项 | 实现文件 | 备注 |
|------|-------|---------|------|
| 企业微信 | `WECHAT_WEBHOOK_URL` | [notification.py:400-420](notification.py) | 自动处理 4KB 限制 |
| 飞书 | `FEISHU_WEBHOOK_URL` | [notification.py:422-450](notification.py) | 自动处理 20KB 限制 |
| Telegram | `TELEGRAM_BOT_TOKEN` + `TELEGRAM_CHAT_ID` | [notification.py:452-480](notification.py) | 支持 Markdown 格式 |
| 邮件 | `EMAIL_SENDER` + `EMAIL_PASSWORD` | [notification.py:482-550](notification.py) | 自动识别 SMTP (QQ/163/Gmail/Outlook) |
| Pushover | `PUSHOVER_USER_KEY` + `PUSHOVER_API_TOKEN` | [notification.py:552-580](notification.py) | iOS/Android 推送 |
| 自定义 Webhook | `CUSTOM_WEBHOOK_URLS` | [notification.py:582-620](notification.py) | 支持钉钉/Slack/Discord/Bark 等 |

### 如何配置

**方式一：环境变量 (本地/.env)**

编辑 `.env` 文件，填入对应配置：

```bash
# 企业微信
WECHAT_WEBHOOK_URL=https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx

# 飞书
FEISHU_WEBHOOK_URL=https://open.feishu.cn/open-apis/bot/v2/hook/xxx

# Telegram
TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrsTUVwxyz
TELEGRAM_CHAT_ID=123456789

# 邮件
EMAIL_SENDER=your_email@qq.com
EMAIL_PASSWORD=your_auth_code
EMAIL_RECEIVERS=receiver1@example.com,receiver2@example.com  # 可选
```

**方式二：GitHub Secrets (GitHub Actions)**

仓库 → `Settings` → `Secrets and variables` → `Actions` → 添加对应 Secret

### 如何扩展新增通道

**场景 1：新增官方支持的通道 (如钉钉)**

使用自定义 Webhook：

```bash
# .env 文件
CUSTOM_WEBHOOK_URLS=https://oapi.dingtalk.com/robot/send?access_token=xxx
```

系统会自动识别钉钉格式并发送 ([notification.py:582-620](notification.py))。

**场景 2：完全自定义通道 (需修改代码)**

1. **编辑 `notification.py`**：
   - 在 `NotificationChannel` 枚举中添加新通道类型 ([notification.py:37-45](notification.py))
   - 在 `NotificationService.__init__()` 中添加配置读取 ([notification.py:114-150](notification.py))
   - 实现发送方法 (参考 `_send_to_wechat()` / `_send_to_telegram()` 等)
   - 在 `send_to_all_channels()` 中调用新方法 ([notification.py:310-380](notification.py))

2. **在 `config.py` 中添加配置项**：
   - 添加新的环境变量读取 ([config.py](config.py))

3. **测试**：
   ```bash
   python main.py --no-notify  # 先跳过推送测试分析流程
   python main.py              # 完整测试
   ```

---

## 🧪 常见开发任务

### 任务 1：新增分析指标/策略

**需求示例**：增加 MACD 指标判断

**修改位置**：

1. **技术分析层** - `stock_analyzer.py`
   - 在 `StockTrendAnalyzer.analyze()` 中添加 MACD 计算 ([stock_analyzer.py](stock_analyzer.py))
   - 在 `TrendAnalysisResult` 数据类中添加新字段

2. **AI Prompt 层** - `analyzer.py`
   - 在 `GeminiAnalyzer.analyze_stock()` 中将 MACD 数据加入 Prompt ([analyzer.py](analyzer.py))
   - 更新 `ANALYSIS_PROMPT_TEMPLATE` (约第 50-150 行)

3. **测试**：
   ```bash
   python main.py --debug --stocks 600519 --no-notify
   ```

### 任务 2：新增推送渠道

参考上面「推送通道说明 → 如何扩展新增通道」。

### 任务 3：修改股票列表/数据源

**修改股票列表**：

- **临时修改** (一次性)：
  ```bash
  python main.py --stocks 600519,000001,300750
  ```

- **永久修改**：
  - 编辑 `.env` 文件：`STOCK_LIST=600519,000001,300750`
  - 或通过 WebUI 管理界面修改 (`http://127.0.0.1:8000`)

**修改数据源优先级**：

编辑 `data_provider/base.py` 中的 `DataFetcherManager.__init__()` ([data_provider/base.py:193-245](data_provider/base.py))：

```python
self.fetchers = [
    EfinanceFetcher(),    # Priority 0
    AkshareFetcher(),     # Priority 1
    TushareFetcher(),     # Priority 2 (可调整顺序或移除)
    BaostockFetcher(),    # Priority 3
    YfinanceFetcher(),    # Priority 4
]
```

**添加新数据源**：

1. 在 `data_provider/` 目录创建新文件 (如 `new_fetcher.py`)
2. 继承 `BaseFetcher` 并实现：
   - `_fetch_raw_data()` - 获取原始数据
   - `_normalize_data()` - 标准化列名
3. 在 `DataFetcherManager` 中注册

### 任务 4：修改定时时间/启用本地调度

**方式一：GitHub Actions (推荐)**

编辑 `.github/workflows/daily_analysis.yml`：

```yaml
schedule:
  - cron: '0 10 * * 1-5'  # 修改此行，格式：分 时 日 月 周 (UTC)
```

示例：
- 每天 18:00 北京时间 = `0 10 * * *` (10:00 UTC)
- 每天 09:00 北京时间 = `0 1 * * *` (01:00 UTC)
- 工作日 18:00 = `0 10 * * 1-5`

**方式二：本地定时任务**

编辑 `.env` 文件：

```bash
SCHEDULE_ENABLED=true
SCHEDULE_TIME=18:00  # 每天执行时间 (24 小时制)
```

然后运行：

```bash
python main.py --schedule
```

程序会持续运行，每天指定时间自动执行。

---

## 🧪 测试与质量

### 代码质量检查

**工具**：Black (格式化) + isort (导入排序) + Bandit (安全检查)

**配置文件**：`pyproject.toml`

**运行命令**：

```bash
# 代码格式化
black --line-length 120 *.py data_provider/ web/

# 导入排序
isort --profile black *.py data_provider/ web/

# 安全检查
bandit -r . -x tests
```

### 单元测试

**需要确认**：项目中暂未发现完整的测试套件 (如 `tests/` 目录或 `test_*.py` 文件)。

**建议**：
- 如需添加测试，推荐使用 `pytest` 框架
- 创建 `tests/` 目录，编写测试用例
- 在 CI (`.github/workflows/ci.yml`) 中集成

### 环境测试

**快速测试脚本**：`test_env.py`

```bash
python test_env.py
```

此脚本会检查：
- Python 版本
- 依赖包是否安装
- 环境变量是否配置
- API 连接是否正常

### CI/CD

**CI Workflow**：`.github/workflows/ci.yml`

**触发条件**：
- Push 到 `main` 或 `dev` 分支
- Pull Request

**检查内容**：
- Python 多版本兼容性测试 (3.10/3.11/3.12)
- 代码质量检查 (Black/isort)
- 依赖安装验证

---

## ❓ FAQ / 排错

### Q1: Cron 定时不触发 (GitHub Actions)

**可能原因**：
- Fork 的仓库默认禁用 Actions
- Cron 表达式时区错误
- 仓库长期无活动被暂停

**解决方案**：
1. 进入 `Actions` → 点击 `I understand my workflows, go ahead and enable them`
2. 确认 Cron 表达式使用 UTC 时区 (北京时间 -8 小时)
3. 定期手动触发或提交代码保持仓库活跃

### Q2: 时区问题 (本地运行显示时间错误)

**症状**：日志时间与本地时间不一致

**解决方案**：

- **Windows**：系统设置 → 时间和语言 → 时区 → 选择正确时区
- **Linux/Docker**：
  ```bash
  export TZ=Asia/Shanghai
  # 或在 Dockerfile 中设置 (已包含)
  ```

### Q3: Token 无权限/API 调用失败

**企业微信/飞书 Webhook**：
- 检查 Webhook URL 是否正确
- 检查机器人是否被移除或禁用
- 查看群聊是否还存在

**Telegram Bot**：
- 确认 Bot Token 和 Chat ID 正确
- 测试命令：`curl -X GET "https://api.telegram.org/bot<TOKEN>/getMe"`
- 确保 Bot 已加入目标群组/频道

**Gemini API**：
- 检查 API Key 是否有效：访问 [Google AI Studio](https://aistudio.google.com/)
- 查看配额是否用尽
- 确认地区是否支持 Gemini API

**邮件 SMTP**：
- 确认使用的是授权码而非登录密码
- QQ 邮箱：需开启 POP3/SMTP 服务并获取授权码
- Gmail：需开启「允许不够安全的应用」或使用应用专用密码

### Q4: 数据源请求失败

**症状**：所有数据源都失败，提示 `Failed to fetch data from all sources`

**可能原因**：
- 网络问题 (防火墙/代理)
- 股票代码格式错误
- 数据源 API 临时维护

**解决方案**：
1. 检查网络连接：`ping www.baidu.com`
2. 确认股票代码格式正确 (沪市：`600xxx`，深市：`000xxx`/`300xxx`)
3. 查看调试日志：`python main.py --debug --stocks 600519 --dry-run`
4. 系统会自动切换 5 个数据源，通常临时失败可重试

**需要代理**：

编辑 `main.py` (第 26-31 行)，取消注释：

```python
if os.getenv("GITHUB_ACTIONS") != "true":
    os.environ["http_proxy"] = "http://127.0.0.1:10809"   # 修改为你的代理端口
    os.environ["https_proxy"] = "http://127.0.0.1:10809"
```

### Q5: 推送失败 (无通知)

**检查清单**：
1. 至少配置一个通知渠道 (检查 `.env` 或 Secrets)
2. 查看日志：`grep "消息推送" logs/stock_analysis_*.log`
3. 测试单个渠道：
   ```bash
   # 编辑 .env，只保留一个渠道
   python main.py --stocks 600519
   ```
4. 检查消息是否过长 (超长会自动分批，但部分平台可能限制频率)

### Q6: AI 分析超时/返回空结果

**Gemini API 超时**：
- GitHub Actions 环境建议增加延时：`.env` 中设置 `GEMINI_REQUEST_DELAY=3.0`
- 检查 API 配额是否用尽

**OpenAI 兼容 API**：
- 确认 `OPENAI_BASE_URL` 正确 (如 DeepSeek：`https://api.deepseek.com/v1`)
- 检查 API Key 是否有余额
- 测试 API 连接：
  ```bash
  curl -X POST "$OPENAI_BASE_URL/chat/completions" \
    -H "Authorization: Bearer $OPENAI_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{"model":"deepseek-chat","messages":[{"role":"user","content":"test"}]}'
  ```

**返回空结果**：
- 检查 Prompt 是否过长 (历史数据过多)
- 调整 `DATA_DAYS` (默认 60 天) → `.env` 中设置 `DATA_DAYS=30`

### Q7: WebUI 无法访问

**症状**：浏览器打不开 `http://127.0.0.1:8000`

**解决方案**：
1. 确认已启动 WebUI：`python main.py --webui-only`
2. 检查端口占用：`netstat -ano | findstr :8000` (Windows) / `lsof -i :8000` (Linux)
3. 修改端口：`.env` 中设置 `WEBUI_PORT=8080`
4. 局域网访问：`.env` 中设置 `WEBUI_HOST=0.0.0.0`

### Q8: Docker 部署问题

**构建失败**：
```bash
# 清理缓存重新构建
docker-compose build --no-cache
```

**容器无法启动**：
```bash
# 查看日志
docker-compose logs -f

# 进入容器调试
docker-compose exec stock-analyzer bash
```

**数据丢失**：
- 确认 `docker-compose.yml` 中 volumes 配置正确
- 数据默认保存在 `./data/`, `./logs/`, `./reports/`

---

## 📚 相关文档

- [README.md](README.md) - 项目说明、特性、快速开始
- [DEPLOY.md](DEPLOY.md) - 部署指南 (Docker/直接部署/Systemd/GitHub Actions)
- [docs/full-guide.md](docs/full-guide.md) - 完整配置指南 (本地 WebUI、高级配置)
- [CONTRIBUTING.md](CONTRIBUTING.md) - 贡献指南 (代码规范、PR 流程)
- [CHANGELOG.md](CHANGELOG.md) - 版本历史 (当前版本：v1.6.0)

---

## ⚠️ 免责声明

本项目仅供学习和研究使用，不构成任何投资建议。股市有风险，投资需谨慎。作者不对使用本项目产生的任何损失负责。

---

**如果遇到本文档未覆盖的问题，请：**
1. 查看 [GitHub Issues](https://github.com/ZhuLinsen/daily_stock_analysis/issues)
2. 提交新 Issue 并附上详细日志
3. 查看调试日志：`logs/stock_analysis_debug_YYYYMMDD.log`

**祝您使用愉快，股市长虹！** 🚀📈
