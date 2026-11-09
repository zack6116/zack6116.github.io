---
layout: post
title: "AI工具开多了项目副本到处都是？一张注册表找目录134秒变0.006秒"
tags: AI文档
---

大家好，我是Zack。

先说个场景：我电脑上有五个 AI 编程工具，Claude Code、Codex、Cursor、WorkBuddy、Hermes，各干各的活。问题来了——它们开项目目录不商量。同一个项目，`~/workspace/` 里一份，`~/Documents/ChatGPT/` 里一份，`~/WorkBuddy/` 里可能还有一份。

上周我想改 wechat-download 这个小工具，打开的是 Documents 里那份。改完一跑，功能不对。折腾半天才发现：我改的是旧副本，真正上线的那份在 workspace 里，已经比我手里这份新了三个 commit。

改错副本这事，一个月碰不上一次，碰上一次就够你骂半宿。

## 我原来的做法：靠记忆，兜底靠 find

没建注册表之前，我的流程是这样的：

想找某个项目，先凭记忆猜个路径，`cd` 过去一看不对，再猜。

猜不出来就全盘搜。我拿 wechat-download 实测了一次，掐表：

```bash
find ~ -type d -name "wechat-download" \
  -not -path "*/node_modules/*" \
  -not -path "*/Library/*"
```

这一条跑了 **134 秒**（机器全盘扫磁盘，慢是正常的），找出 2 份。

但找到只是第一步。哪份是新的？还得挨个 `git log`：

```bash
cd ~/workspace/wechat-download && git log --oneline -3
# fd980b6 fix: CI Node 升到 22
# 0c28cc0 fix: wrangler-action 固定 wrangler 版本
# 5049ffa ci: push 到 main 自动部署

cd ~/Documents/ChatGPT/wechat-download && git log --oneline -3
# b661440 feat: 整号抓取——落地页浏览器端 BFS 遍历
```

workspace 版停在 `fd980b6`，Documents 版停在 `b661440`，差 3 个 commit。连 README 都不一样——旧版压根没有「自动部署」那一节，新版有 GitHub Actions 自动部署到 Cloudflare 的完整说明。

这还算好的，至少 git log 能比出来。更坑的是那种两边都有未提交改动的：你根本说不清哪份是「对的」，只能靠回忆「我上次在哪个工具里改的来着」。

这些动作加起来：find 2 分钟、git log 两遍、diff 一遍，五分钟没了，结论还是「大概齐」。

## 现在的做法：一张注册表，一条规矩

后来我搭了个叫 agent hub 的东西，就一个目录三个文件，放在 `/opt/agent-hub/`：

**`REGISTRY.md` —— 注册表本体。** 一个扫描脚本（`scan.py`，就几十行 Python，遍历几个常用根目录找 `.git`）自动生成，每个项目一行：

```markdown
| `wechat-download` | `/Users/zack/workspace/wechat-download`
  | git/README.md | zack6116/wechat-download | 2026-10-13 |
| `wechat-download` | `/Users/zack/Documents/ChatGPT/wechat-download`
  | git/README.md | zackzhangkai/wechat-download | 2026-09-30 |
```

项目名、权威路径、类型、git 远端、最近改动，一排全齐。我机器上扫出来 72 个常驻项目。注意看两行的 git 远端：一个是新账号 `zack6116`，一个还是旧账号 `zackzhangkai`——旧副本连远端都是老的，一行就能看穿。

**`MANUAL.md` —— 裁决记录，人写的。** 光知道有两个副本不够，得定谁是权威：

```markdown
### wechat-download（已裁决 2026-11-08）
以 ~/workspace/wechat-download 为准。已上线到 Cloudflare 的就是这一版。
Documents/ChatGPT 那份是旧快照，勿在其上开发。
```

**`BOOTSTRAP.md` —— 总纲。** 开工三步：读注册表定位目录、查 skill 索引、超七天没更新就重新扫描。

然后立了一条规矩，就一条：**干活前先查表，不另起副本。**

现在的查法：

```bash
grep "wechat-download" /opt/agent-hub/REGISTRY.md
```

掐表实测：**0.006 秒**。两行副本直接蹦出来，谁新谁旧、远端是谁，一眼看完。两个都列着拿不准，再去 MANUAL.md 看裁决——一行字。

给 AI 干活也吃这套。我把 BOOTSTRAP.md 挂进了 Claude Code 的全局配置（`~/.claude/CLAUDE.md` 里一行 `@/opt/agent-hub/BOOTSTRAP.md`），这样每个新会话开局自动读注册表。我实测过：裸开一个 claude 问「wechat-download 怎么部署的」，**31.8 秒**直接答对，部署平台、触发方式、依据文件列得清清楚楚——它没花时间在全盘找目录上，开局就先读了表。

## 想搭一份？最小步骤在这

不用照搬我的，一个目录一个脚本就能起步。五分钟的事。

**第一步，写个扫描脚本**，列出你常用目录下的所有 git 项目：

```bash
#!/bin/bash
# scan.sh - 遍历常用根目录，列出项目名和路径
for root in ~/workspace ~/Documents/ChatGPT ~/WorkBuddy; do
  find "$root" -maxdepth 2 -name ".git" -type d 2>/dev/null | \
  while read -r gitdir; do
    dir=$(dirname "$gitdir")
    remote=$(git -C "$dir" remote get-url origin 2>/dev/null || echo "无远端")
    echo "| \`$(basename $dir)\` | \`$dir\` | $remote |"
  done
done
```

跑一次，输出直接贴进 `REGISTRY.md`，表就有了。`maxdepth 2` 按你的目录习惯调，扫得太深会把别人仓库的示例项目也捞进来。

**第二步，发现副本就当场裁决。** 表里出现同一个项目名两行时，当场比 git log，定谁权威，往 `MANUAL.md` 写一行「以谁为准 + 理由」。裁决过一次的项目，以后不用再想。

**第三步，把表喂给你的 AI 工具。** 各家吃法一行配置：

- Claude Code：`~/.claude/CLAUDE.md` 里加一行 `@/opt/agent-hub/BOOTSTRAP.md`（或你的表路径），每个会话自动读。
- Codex：`~/.codex/AGENTS.md` 里贴上同样的指引。
- Cursor：项目根目录的 `.cursorrules` 或 Rules 里写「开工先读注册表」。

原则一样：让 AI 的**开局动作**里多一步「查表」，它就不会自己满盘找目录，更不会随手 `mkdir` 一个新副本。

**第四步，定个刷新纪律。** 我的做法是表头写生成时间，超过七天没更新就重跑一次扫描。脚本生成的表不怕旧——重扫一遍就新了，怕旧的是 MANUAL 里的裁决，那个要跟着项目走，项目废弃了裁决也划掉。

## 前后对比

| 动作 | 原来 | 现在 |
|---|---|---|
| 找项目目录 | 全盘 find 134 秒 | grep 0.006 秒 |
| 判断哪份权威 | git log + diff 逐份比对，几分钟 | 查 MANUAL 裁决，一行字 |
| AI 开局 | 自己找目录翻文件 | 先读表直接定位 |
| 改错副本风险 | 靠记忆裸奔 | 远端账号名+裁决记录双重确认 |

诚实说一句：134 秒的 find 是全盘扫磁盘，本来就是个一次性的笨办法，拿它跟 0.006 秒比有点欺负人。真正的差距不在速度数字上，在于「每次找项目都要重新猜一遍」变成了「一次裁决，长期有效」——134 秒你可以忍，改错副本白干一下午你忍不了。

维护成本也交代一下：重扫一次就几秒钟，注册表超过七天没更新、或者新建了项目，跑一次 `hub scan` 就行。注册表文件头写着「自动生成勿手改」，要改条目去改扫描脚本，要补裁决写 MANUAL——不然下次一扫描你的手改就没了。

还有一个它管不了的事得说清：**注册表只告诉你该用哪份，不帮你同步两份。** Documents 里那份旧快照还躺在原地，表没删它、也没把 3 个 commit 差距补上。要不要归档旧副本、什么时候归档，得你自己动手。表解决的是「别再改错」，不是「自动整洁」。

## 两条可以搬走的

**第一，靠记忆找第三遍的东西，就该变成一张表。** 上个月我写过「干到第三遍的活就该写成脚本」，这条是它的表格式版本：脚本解决「重复执行的活」，表解决「重复查的信息」。项目路径、服务器地址、账号配置，凡是你发现自己在聊天记录里往上翻的，都算。

**第二，表管「在哪」，裁决要单独记「以谁为准」，理由必须写上。** 光有一张列出 72 个项目的表，wechat-download 照样两个都在表里。真正救命的是 MANUAL 里那句「以 workspace 版为准，因为线上跑的是这版」。没写理由的规矩，过俩月你自己都不服。

你的项目目录乱成什么样了？留言区聊聊。如果同样被多工具多副本折磨，可以照着上面三个文件的思路搭一份，扫描脚本真不用复杂，能列出「目录名 + 路径 + git 远端」就够用了。
