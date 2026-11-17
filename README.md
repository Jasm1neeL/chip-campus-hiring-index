# 芯片目标实验室 / 顶会 / 赛事合辑（网页版）

一个单文件静态站点，内容来自 `GPU软件栈实习岗_实验室与赛事sourcing清单_核验版_20261116.md`。
无框架、无构建、无外部依赖，`index.html` 双击即可打开。

---

## 一、现在就能用的链接

| 用途 | 地址 | 状态 |
|---|---|---|
| **正式链接（推荐转发）** | https://jasm1neel.github.io/chip-campus-hiring-index/ | 永久固定，国内网络直连可用；学生名录加 `#students` |
| Cloudflare 镜像（海外 / 代理网络） | https://chip-campus-hiring-index.jasm1neel.workers.dev/ | 已部署成功，但国内网络到 Cloudflare 边缘节点常被阻断，打不开属正常现象 |
| 本机 | http://127.0.0.1:8000/ | 需先运行 `启动.ps1` |
| 局域网（同一 WiFi 的手机 / 电脑） | http://<本机 IP>:8000/ | 需先运行 `启动.ps1` |

> 两个线上地址都来自同一个 GitHub 仓库 `Jasm1neeL/chip-campus-hiring-index` 的 `main` 分支。
> 每次执行 `push-github.ps1` 提交后，**GitHub Pages 与 Cloudflare 会各自自动重新构建发布**，不需要手动上传，也不需要本机开机。
> 临时隧道 `trycloudflare.com` 已停用 —— 那种地址会随进程回收且无法找回，不要再转发。

---

## 二、怎么启动

```powershell
cd "C:\Users\lixinjie\Documents\默认项目\sourcing-site"
powershell -ExecutionPolicy Bypass -File .\启动.ps1
```

脚本会做三件事：
1. 在 8000 端口起一个静态服务器（PowerShell 原生，不需要 Python / Node）；
2. 检查有没有 `cloudflared.exe`（没有就从镜像自动下到临时目录）；
3. 起公网隧道，并把三个链接打印出来。

**首次运行** Windows 可能弹防火墙提示 → 选「允许」，否则局域网设备访问不了。

停止：在该窗口按 `Ctrl+C`，或直接关窗口。

---

## 三、页面上有什么

| 模块 | 内容 |
|---|---|
| **机构库** | 15 个目标机构 / 团队。可按岗位（①②③④）、方向筛选，支持关键词搜索。**没有「待复核」条目**，留下的每条都已核过官网 |
| **学生名录** | 32 位**已核验在读**的博士生 / 硕士生（清华 PACMAN、上交 ReArch、北大 CECA），含姓名、学校、导师、年级、GitHub / 邮箱与在读证据链接。只收官方页面明确写「在读」的人；已毕业的一律剔除 |
| **岗位组合** | 4 个岗位各自的「最小扫描组合」：2 机构 + 1 社区 + 1 赛事 |
| **顶会顶刊** | 30 个会议 / 期刊，点击直达 DBLP 检索页（按作者单位筛人） |
| **赛事** | 4 个赛事，含 2026 赛季时间与官方入口 |
| **开源社区** | 20 个项目的官方仓库 / 主页，按编译器 / 算子 / 框架 / 芯片四条线分组 |

**学生名录的口径**：只收官方页面能证明「现在还在读」的人。优先用实验室 People 页（如清华 PACMAN 的 PhD / MS 分栏，页面 2026-11-01 构建）；其次用学生本人主页里写的「X 年级博士生 / 预计 X 年毕业」；再次用 2025–2026 年论文的作者单位 + 公开邮箱。已毕业、只出现在 Alumni 栏、或主页写明已入职的一律剔除（例如北大石弼钊主页明确写了 2026 年 7 月毕业，因此没有收录）。联系方式只放本人公开的 GitHub / 邮箱，找不到就写「未公开」，不做猜测。

每张机构卡片点「详情」会展开：
- **实验室官网**入口（点开是实验室主页，不是某个导师）
- **学生 / 团队名录**官方指路（能直接列出在读学生的机构，已在详情里追加「在读学生 · 已核验」名单）
- **导师名单**：每个名字都可点，直达本人介绍页（全部逐一打开核验过，页面含本人姓名）
- **为什么对口 / 建议动作**
- **链接健康度**：每周一 10:00 自动检测的结果，含一键实测
- **实时复核按钮**：跳转到该官网的站内检索（`site:域名 姓名 招生`）
- **跟进状态**：未联系 / 已发邮件 / 已回复 / 已约面试 / 不合适（存在浏览器本地，刷新不丢）

顶部还有：
- **复制清单** —— 按当前筛选结果导出 Markdown，可直接粘进微信 / 邮件
- **导出 CSV** —— 带跟进状态，Excel 打开无乱码

---

## 四、永久链接的部署与更新（已配置完成）

### 当前架构

```
本地文件  --push-github.ps1-->  GitHub 仓库 main  --+--> GitHub Pages   → jasm1neel.github.io（主用）
                                                    +--> Cloudflare     → workers.dev 镜像
```

- 仓库：`https://github.com/Jasm1neeL/chip-campus-hiring-index`（Public，`main` 分支）
- GitHub Pages：仓库 Settings → Pages → Source = `main` / `/root`
- Cloudflare：Workers & Pages → 已连接同一仓库；**Build command 留空**、Build output directory = `/`
- 凭据存放在本机 `deploy.config.json`（`githubToken`），**不要提交到仓库**

### 日常更新（唯一需要做的）

```powershell
cd "C:\Users\lixinjie\Documents\默认项目\sourcing-site"
powershell -ExecutionPolicy Bypass -File .\push-github.ps1 -Message "更新说明"
```

脚本会把 `index.html` / `refresh-status.json` / `README.md` 提交到 `main`，两个线上地址在 30–60 秒内自动更新。
站内 `fetch("refresh-status.json")` 用的是相对路径，所以带 `/chip-campus-hiring-index/` 前缀也能正常读到刷新状态。

### 直接传到 Cloudflare（备用，不走 Git）

```powershell
powershell -ExecutionPolicy Bypass -File .\deploy.ps1
```

需要 `deploy.config.json` 里另配 Cloudflare 的 `accountId` + `apiToken`（权限 Account → Cloudflare Pages → Edit）。
注意：当前项目是 Git 集成的，Cloudflare 不支持把它切回 Direct Upload，所以这个脚本主要用于另建项目/应急。

### 为什么主用 github.io 而不是 Cloudflare

实测本机到 Cloudflare 边缘节点被阻断：`workers.dev` 域名被解析到非 Cloudflare IP，直连正确 IP 会被 reset，`pages.dev` 连接超时；而 `github.io` 稳定 200 / 1–3 秒。
两边都在自动部署，哪边能打开就用哪边；换网络或挂代理后 Cloudflare 链接可能恢复。

---

## 五、文件说明

| 文件 | 作用 |
|---|---|
| `index.html` | **站点本体**（HTML + CSS + JS + 数据，全在一个文件里，约 77 KB） |
| `serve.ps1` | 本地静态服务器（PowerShell 原生实现，监听 0.0.0.0:8000） |
| `启动.ps1` | 一键：起服务器 + 起公网隧道 + 打印链接 |
| `refresh.ps1` | 自动刷新脚本：重测全部链接，写入 `refresh-status.json` |
| `refresh-status.json` | 最近一次检测结果（时间、HTTP 状态、耗时、内容哈希、是否变动） |
| `refresh.log` | 每次检测的明细日志 |
| `push-github.ps1` | 把站点提交到 GitHub（用 Contents API，本机无需装 Git）；提交后两个线上地址自动更新 |
| `deploy.ps1` | 备用：走 Cloudflare API 直接上传（需另配 Cloudflare Token） |
| `deploy.config.json` | 本机凭据（GitHub Token，可选 Cloudflare 配置）；**已排除在发布内容之外** |
| `README.md` | 本文件 |

### 每周一 10:00 自动刷新（已配置）

计划任务名：`GPU-Sourcing-Weekly-Refresh`

```powershell
# 查看状态
Get-ScheduledTask -TaskName "GPU-Sourcing-Weekly-Refresh"
Get-ScheduledTaskInfo -TaskName "GPU-Sourcing-Weekly-Refresh"

# 立即手动跑一次
Start-ScheduledTask -TaskName "GPU-Sourcing-Weekly-Refresh"

# 改时间 / 删除
Set-ScheduledTask -TaskName "GPU-Sourcing-Weekly-Refresh" -Trigger (New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday -At 10:00)
Unregister-ScheduledTask -TaskName "GPU-Sourcing-Weekly-Refresh" -Confirm:$false
```

它会扫描 `index.html` 里全部官网链接（当前 80 条），逐条请求并比对内容哈希，把结果写进 `refresh-status.json`。超时或 5xx 会自动重试一次。
页面右上角显示「上次刷新时间 · 失效 · 超时 · 变动」，每条链接旁有状态点。GitHub 链接因本机网络常被拦截，不计入「超时」数（鼠标悬停可看说明）。

---

## 六、关于「实时」

浏览器有同源策略，网页**不能**直接抓取其它网站的正文——任何声称「实时同步官网内容」的静态页面，
要么靠自己的后端去爬（有法律与稳定性风险），要么是假的。

所以这里的做法是：

1. **每条信息都带原始来源链接**，点开就是官网原文，可追溯、可验证；
2. **一键站内检索**：`site:官网域名 + 姓名 + 招生`，直接看该导师今年的方向与名额；
3. **可达性检测**：浏览器实测该域名在你当前网络是否连得上；
4. **每周一 10:00 自动刷新**：本机计划任务逐条重测官网链接，记录状态、耗时与内容哈希；
   内容有变化的链接会在页面上标黄点，提示你点开看看；
5. **数据版本戳**：页面底部写明核验日期。

浏览器有同源策略，网页**不能**直接抓取其它网站的正文。所以「自动刷新」刷新的是**链接可达性与内容变动信号**，
正文仍然直达官网，不在本地镜像。这比伪造一份"实时数据"更可靠。

---

## 七、已知限制

- 公网隧道地址每次启动都会变；要固定地址，用第四节任一方案。
- 跟进状态存在**浏览器 localStorage**，换设备 / 清缓存会丢；要跨设备同步得配后端。
- GitHub 相关链接在部分网络下需要代理，页面里已标注；超时会被记为「超时」，不算链接失效，且不计入顶部的超时数。
- 自动刷新依赖本机开机并登录：电脑关机 / 休眠时，周一 10:00 的任务会顺延到下次开机后执行。
- 页面上的状态点反映的是**本机网络**的检测结果（当前 80 条），别人打开网页时看到的是同一次检测的快照。
- 学生名录目前覆盖 3 个机构（清华 PACMAN、上交 ReArch、北大 CECA），因为这 3 家有可核实「在读」的公开名单；其余机构未公开学生名录（或为军工 / 保密单位），站内不补造。32 人中 7 人有本人公开的 GitHub / 邮箱，其余只能走官方个人页。
- 「在读」是**核验时点**的判断，学生毕业 / 换组后需要下一轮核对；这也是每周刷新之外，建议每学期人工过一遍学生名录的原因。
