# 界面截图素材清单

> **采集时间**：2026-11-23
> **来源实例**：NAS `192.168.50.50:9080`，镜像 `lichangzhou/docker-link:v1.2.16`
> **实况数据**：24 个容器（22 运行中）· 38 个镜像 · 6 个卷 · 11 个网络 · 29 个 compose 文件 · Docker Engine 28.5.2
> **规格**：1600 × 1000 CSS 像素 @ 2x DPI（实际 3200 × 2000 PNG），全部为**真实运行实例**，非设计稿
> **采集方式**：Playwright + Chromium（CDP `Page.captureScreenshot`），逐个视图切换后截图

---

## 主推图（README 首屏使用）

| 文件 | 视图 | 展示要点 |
|---|---|---|
| `02-nav.png` | 导航 / 卡片墙 | **最佳主图**。24 个容器卡片，自动匹配图标（plex / jellyfin / homeassistant / evolink / docker-link 鲸鱼图标），右上角标语时钟 |
| `01-dashboard.png` | 仪表盘 | 容器运行率 22/24、镜像 38、卷 6、Docker 28.5.2、宿主 CPU 4% / 内存 35.5GB·62.4GB / 磁盘 729.5GB·3.6TB、实时事件流 |
| `03-containers.png` | 容器 | 列表/卡片双视图、4 种排序、批量操作、任务进度条、真实容器列表 |
| `21-compose-detail.png` | Compose | **核心卖点图**。左侧 29 个项目，右侧 YAML 编辑器 + `up/down/restart/build/pull` 按钮 —— 面板与磁盘 yaml 是同一份事实来源 |
| `09-terminal.png` | Web 终端 | 真实 `docker exec` PTY 会话：`uname -a`、`ls -la /`、`docker ps` 实际输出、可连续输入的命令行 |
| `13-templates.png` | 模板市场 | 11 个预置模板（Emby / Jellyfin / Plex / qBittorrent / MoviePilot / Nginx / Watchtower / Portainer CE / Lucky / Playable / EvoLink），带官方图标 |
| `08-files.png` | 文件管理 | 面包屑 `/vol1/1000/docker`，真实目录树（29 个 compose 项目目录），支持浏览/编辑/上传/下载 |
| `20-container-detail.png` | 容器详情 | 端口映射、挂载、环境变量、重启策略、镜像摘要 |
| `10-agents.png` | 远程节点 | **4 步接入引导 + 「它是怎么工作的」技术说明**（表格为空是因为演示实例未配置远程节点） |

## 功能补充图

| 文件 | 视图 | 展示要点 |
|---|---|---|
| `04-images.png` | 镜像 | 仓库:TAG / ID / 大小 / 操作，含 `playable-server:latest` 等真实镜像 |
| `05-volumes.png` | 卷 | 卷名 / 驱动 / 挂载点 |
| `06-networks.png` | 网络 | 网络名 / 驱动 / 子网 / 容器数 |
| `07-compose.png` | Compose 列表 | 29 个 compose 文件一览（文件模式） |
| `11-icons.png` | 图标 | 图标库、官方图标同步通道 |
| `12-monitor.png` | 监控 | 定时巡检与自动恢复配置 |
| `12b-monitor-autorecover.png` | 监控 | 自动恢复策略区（`always` / `unless-stopped` / `on-failure` 崩溃自动拉起） |
| `14-deploy.png` | 部署控制台 | 粘贴 compose 一键部署、端口冲突自动避开 |
| `15-backup.png` | 备份 | 全量备份 / 定时备份 / 导出 / 跨机迁移 |
| `17-settings.png` | 设置 | Compose 目录、文件管理器根目录、主题、访问用户名密码、失效恢复码 |
| `22-settings-recovery.png` | 设置 | 失效恢复码 / 微信恢复码上传区 |
| `19-license.png` | 许可证 | 专业版 License 校验（设备数、到期时间） |

---

## 使用建议（按平台）

| 平台 | 建议用图 | 理由 |
|---|---|---|
| **什么值得买** | `02-nav`（头图）+ `21-compose-detail` + `09-terminal` + `03-containers` | 值友看的是「界面好不好看 + 能不能干活」，头图用卡片墙最抓人；配图需 4 张以上、正文 1500 字左右 |
| **linux.do** | `02-nav` + `09-terminal` + `10-agents` | 佬友看技术实现，终端与远程节点说明是重点 |
| **恩山** | `03-containers` + `08-files` | 纯技术贴，配 NAS 实景截图更有说服力 |
| **掘金 / CSDN** | `21-compose-detail` + `12-monitor` | 技术向，配「文件即真相源」「自动恢复」两张最贴题 |
| **阮一峰周刊 / HelloGitHub** | `02-nav` 单图即可 | Issue 只放一张，别刷图 |
| **Reddit / HN** | `02-nav` + `21-compose-detail` | 海外用户先看整体观感，再看差异化功能 |
| **视频封面（B站/抖音）** | `02-nav` 裁切 | 卡片墙颜色对比强，缩略图辨识度高 |

---

## 注意

- 截图里可见 **NAS 真实目录名与容器名**（含 `moviepilot-v3`、`mihomo`、`homeassistant` 等）。这些是公开服务的通用名，不涉及隐私，但**内网 IP、域名、账号信息已确认未出现在任何一张图中**。
- 截图中的 `19-license.png` 含激活码占位符 `DL-XXXX-XXXX-XXXX`，用于说明「有商业版 License 机制」，**不适合突出展示**。
- 已剔除两张图：访问日志页（含一条企业微信推送的 SSL 报错，不适合对外）与多用户页（实例未启用，页面为空）。
- 重新采集时统一用：

  ```bash
  # 需要 playwright + chromium
  python3 shoot.py   # 逐个 .nav[data-view] 切换 → CDP Page.captureScreenshot
  ```

  注意 `page.screenshot()` 在本页面会因 CSS 动画卡死，必须用 CDP `Page.captureScreenshot`。
