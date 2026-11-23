# linux.do 专用版（公益推广）

> **发布前置**
> 1. 账号需通过人工审核（写约 50 字真实自我介绍，说明「自己写了个开源 Docker 面板，想找人一起用」即可，通过率约 20%）
> 2. 建议先把信任等级升到 **TL1**（进 5 个话题、读 30 帖、累计阅读 10 分钟），TL0 单帖限 2 链接 / 1 图
> 3. 版块选 **「扬帆起航」**，标签选 **「公益推广」**
>
> **合规自查表**
> - [x] **无任何 AFF / 联盟 / 返佣链接**（2025-08-07 起全站禁止，不分版块）
> - [x] **不引流到其他社区 / 社群**（不写「去恩山看」「加我 QQ 群」「扫码」）
> - [x] 链接 **只有 1 个**：GitHub 项目地址
> - [x] 无微信 / QQ / TG / 二维码
> - [x] 语气真诚，**有动机、有真实使用场景、坦诚局限**
> - [x] CTA 是「欢迎 star/issue/PR、欢迎拍砖」，**不是求点击**
> - [x] 生态互推用 `生态互推片段库.md` 片段 A：另两个项目**只写仓库名代码样式**，**不加大链接**（保 1 链接上限），措辞是「同作者的另外两个项目」而非导流活动

---

## 标题

```
[公益推广] Docker-Link：自写单容器 Docker 面板，Compose/文件/终端/远程节点/企业微信告警
```

---

## 正文（可直接粘贴）

### 先说动机

我自己 NAS 上常驻 20+ 容器（Jellyfin / Emby / qBittorrent / alist / HA / mihomo…），之前用 Portainer 用了半年，撞到几个让我实在难受的点：

1. **Compose 文件和面板不是一回事** —— Portainer 的 Stack 存自己数据库，我在面板上加容器，硬盘上的 compose 文件不会跟着变，下次 `compose up -d` 直接给我覆盖回去。后来我每次改 compose 都得 SSH 手改文件再回面板点部署。
2. **文件管理等于没有** —— 看 compose 内容只能 `cat`，传文件得开 SFTP。
3. **CE 版远程节点很痛苦** —— 得在网页里一个个敲私钥、端口、密码。
4. **界面全英文**。

周末写了两天，起了个名 **Docker-Link**，自己部署自己用，跑了两三个月。

### 做了什么

- **容器 / 镜像 / 卷 / 网络 / 端口** 完整 CRUD，日志、终端、重启
- **Compose 项目直接扫硬盘目录** —— 面板和 compose 文件是同一份事实来源，不会出现「界面改了文件没改」
- **文件管理器** —— 浏览、在线编辑、上传、下载
- **Web 终端** —— `docker exec` + PTY，真交互式 shell，不是伪终端
- **多节点** —— Agent 模式（HTTP + Token）或纯 SSH 通道，二选一
- **镜像更新三层兜底** —— 加速源 → dockerd 直连 → 容器内代理 `docker load`；判断有无新版**比对 digest 而不是 tag**（tag 不变但底层镜像更新这种情况，很多面板会漏）
- **企业微信告警**（容器挂了 / 更新完成推群）
- **图标自动匹配 + 手动上传**

技术栈：FastAPI + docker-py + SocketIO，前端是**单文件** `index.html`（零构建、镜像小）。源码在 GitHub 上开源，MIT。

### 三条命令部署

```bash
docker run -d --name docker-link --restart always -p 9080:9080 \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /opt/compose:/opt/compose:rw \
  -v docker-link-data:/data \
  -v docker-link-config:/app/config \
  -e TZ=Asia/Shanghai \
  lichangzhou/docker-link:latest
```

### 局限（也得说清楚）

- 目前只发了 **amd64** 镜像，ARM 机器用不了，得自己构建
- **默认无内置认证**：挂了 `docker.sock`，能操作 Docker 基本≈宿主机 root。我自己设了 `ADMIN_USER`/`ADMIN_PASSWORD` 且只在局域网访问，**不建议暴露公网**
- 单文件前端，代码量大了之后维护成本上来了
- 项目很年轻，接口还会变

### 地址

开源项目，欢迎佬友 star / issue / PR，也欢迎拍砖说说你们怎么用：

```
https://github.com/lichangzhou/Docker-link
```

### 顺便（同作者的另外两个项目）

写面板的过程中我发现，光把容器跑起来不够——容器起来了，媒体库本身还是乱的。于是又陆续写了两个配套项目，也是我一个人做的：

**EvoLink** 管媒体库治理：缺口比对、归档刮削、权重洗板、302 换链。**Playable** 管播放端：Android / Windows / iOS，直接播 `.strm` 库，自动跟随 302 跳转。

仓库分别在 `lichangzhou/Evolink` 和 `lichangzhou/Playable`，都在同一个 GitHub 账号下。有兴趣可以翻翻，不用凑一套用。

纯粹自用工具做出来的，欢迎提需求、做 PR、指出哪里写得烂。
