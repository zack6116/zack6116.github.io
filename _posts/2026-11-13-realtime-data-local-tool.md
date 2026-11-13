---
layout: post
title: "问AI要实时股价它给了旧价？配个本地脚本11秒拉到真价"
tags: AI文档
---

大家好，我是Zack。

先说个场景。我想看几只美股现在啥价，顺手把问题丢给 AI：NVDA、AMD、SOXX 现在多少钱一股？各涨跌多少？

看着挺利索，几十秒就回了张表格。但一核对价格，我后背发凉——有的标的价格是旧的。不是差一位小数那种错，是**一个月前的旧价**。

今天做了组对照实验，把这事掰开看了看。

## 我原来的做法：直接问 AI

我在一个空目录里，用同一个问题让 AI 查了三轮行情，全部掐表。

第一轮，问 NVDA、AMD、SOXX 三标的现价和涨跌：

**109 秒**，答对了。现价、涨跌额、涨跌幅全对，来源新浪财经。

第二轮，换 AAPL 和 TSM：

**118 秒**，价格是旧的。AAPL 给的是 10 月 16 日收盘的 343.46，当天实际 354.88；TSM 给的是 11 月 6 日的 375.44，实际 405.49。都差了快一个月。AI 自己倒是诚实，表格下面补了句「缓存较旧，不是今天的实时价」。

第三轮，只问 AAPL 一个：

**56 秒**，这次对了，354.88，从 stockanalysis.com 抓的。

三轮汇总：56 到 118 秒，对两轮错一轮。慢我忍了，真正的问题是——你问的是实时价，它有三分之一的概率甩给你缓存旧价，还只在说明里轻描淡写提一句。行情这种东西，旧一个月，后面的判断全废。

## 换个做法：给 AI 配个本地行情脚本

今天早上我让 Cursor 干了件事：装 Google 新出的 Antigravity CLI（命令叫 agy，装完 1.5.7），顺手写一个直接拉 Google Finance 行情的脚本，再注册成 MCP。

这里先解释下 MCP，大白话版：给 AI 配工具的统一插座。你把「查行情」这个能力注册进去，AI 要用的时候直接调，不用每次自己满世界找数据源。

分三步，都能抄。

第一步，脚本本体。逻辑就一件事：urllib 直接请求 Google Finance 的报价页，把页面里 `AF_initDataCallback` 数据块用正则抠出来，解析成现价、涨跌、前收、日区间、市值、板块。让 AI 写，195 行，几分钟的事：

```python
def fetch_quote(symbol, exchange=None):
    sym, ex = parse_symbol(symbol)
    url = f"https://www.google.com/finance/quote/{sym}:{ex}?hl=en"
    req = urllib.request.Request(url, headers={"User-Agent": UA})
    html = _opener().open(req, timeout=30).read().decode("utf-8", "replace")
    raw = _af_data(html, "ds:10")   # 正则抠 AF_initDataCallback 数据块
    return parse_quote(raw, sym, ex)
```

第二步，配代理。本机访问 google 得走代理，脚本里用一个 opener 读环境变量，代理挂了也不至于裸连超时：

```python
def _opener():
    proxy = os.environ.get("https_proxy") or os.environ.get("HTTPS_PROXY")
    handlers = []
    if proxy:
        handlers.append(urllib.request.ProxyHandler(
            {"http": proxy, "https": proxy}))
    return urllib.request.build_opener(*handlers)
```

第三步，注册成 MCP。写到 `~/.gemini/config/mcp_config.json`：

```json
{
  "mcpServers": {
    "google-finance": {
      "command": "python3",
      "args": ["~/workspace/agy-finance/scripts/google_finance_mcp.py"],
      "env": {
        "https_proxy": "http://127.0.0.1:7890",
        "http_proxy": "http://127.0.0.1:7890"
      }
    }
  }
}
```

最后包一个 4 行的壳命令 `brief`，把代理和调用封在一起，以后一条命令搞定：

```zsh
#!/bin/zsh
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890
exec python3 "$HOME/workspace/agy-finance/scripts/google_finance.py" --brief "$@"
```

## 前后对比

同样的三个标的，跑一下：

```
./brief NVDA AMD SOXX
```

**11 秒**，三个全出，真实输出长这样：

```
## NVIDIA Corp (NVDA:NASDAQ)
- 现价: 166.19 USD（-7.54 / -4.34%）
- 前收: 173.73
- 日区间: 148.8 – 167.14
- 市值: 4.005e+12
## Advanced Micro Devices Inc (AMD:NASDAQ)
- 现价: 585.3 USD（+32.40 / +5.86%）
## iShares Semiconductor ETF (SOXX:NASDAQ)
- 现价: 154.99 USD（-0.41 / -0.26%）
```

多跑几次，耗时在 11 到 29 秒之间波动（Google 那边偶尔掐连接）。

摆在一起看：

| | 裸问 AI | 本地脚本 |
|---|---|---|
| 耗时 | 56~118 秒 | 11~29 秒 |
| 数据新鲜度 | 三轮错一轮，错的是一个月前的价 | 每次从 Google Finance 现拉 |
| token 花销 | 现场找数据源，读一堆网页 | 零 |

最有说服力的是 TSM。同一个标的同一天：裸问 AI 给 375.44（11 月 6 日的价），脚本拉到 405.49（当天的价）。差 30 美元，差了 7.5%。

还有个意外收获。agy 的 Google 登录今天还没走完（卡在通行密钥那步），但脚本和 MCP 压根不依赖登录态。等登录的功夫，行情已经能拉了——工具链的活不用串行等。

## 两条通用原则

一，**要实时数据，别让 AI 现场找数据源**。给它配个本地工具（脚本或 MCP 都行），时间省一半以上，数据永远新鲜，还不烧 token。这种「数据获取」脚本一次投入长期用，写一个几分钟，值。

二，**AI 给的数字要核来源**。它不知道自己给的是旧的——第二轮它把 10 月 16 日的价当答案端上来，还觉得自己答完了。越是「看起来确定」的数字，越要问一句：这数哪来的，什么时候的。

## 局限，照实说

Google Finance 网页结构一改，正则就得跟着改，这是脚本方式的固有维护成本。今天实测多标的合跑偶尔被 reset 连接，拆开单个跑就正常。另外这只是拿行情数据的方法，不构成任何投资建议，我也没荐任何标的。

你平时让 AI 查实时数据，踩过什么坑？留言区聊聊。

公众号：**Zack说AI**
