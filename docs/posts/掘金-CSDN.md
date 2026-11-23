# 掘金 / CSDN 专用版（技术向，可带外链）

> **平台特性**：这两个是「搜索消费型」平台，SEO 权重高但站外转化低。
> **写法定向**：不要写成产品软文，写成**技术实现复盘** ——「Docker 面板是怎么做出来的：单文件前端 + 三层镜像更新兜底」。读者冲着技术点来的，顺带知道有这个东西，转化反而更好。
> **外链策略**：正文末尾放 GitHub + Docker Hub（技术社区对来源链接宽容）。**不要**在正文中间反复插链接。
> **生态互推**：用 `生态互推片段库.md` 片段 C，末尾追加 EvoLink / Playable 两个仓库链接（**+2 外链**，技术社区对多外链宽容）。
> **关键词布局**（CSDN 必做）：Docker 可视化面板、Portainer 替代、Docker 管理面板、docker.sock、docker compose 可视化、NAS Docker 管理、Web 终端 docker exec、镜像 digest 比对。

---

## 标题（选一个）

1. `从零实现一个 Docker 可视化面板：单文件前端 + 三层镜像拉取兜底 + digest 比对`
2. `Docker 管理面板怎么选？我写了一个单容器部署的：Docker-Link 实现笔记`
3. `别再只看 tag 了：镜像更新检测的正确姿势（digest 比对）与三层拉取兜底实践`

---

## 正文（可直接粘贴，掘金带 Markdown 语法）

## 写在前面

做这个项目的起点很朴素：我自己的 NAS 上跑着 20 多个容器，Portainer 用到第半年实在受不了——**面板里的 Stack 和硬盘上的 compose 文件是两套东西**，我在 UI 里加的容器不会写回 yaml，下次 `docker compose up -d` 就被覆盖回去了。

于是决定自己写一个：**Docker-Link**，单容器部署，前端零构建。这篇不写产品介绍，写实现里几个我觉得值得说的点。

---

### 一、为什么前端只用了一个 index.html

很多 Docker 面板前端是 React/Vue + 打包产物，镜��里塞一个 `node_modules` 级别的 `dist`。我选的是**单文件 HTML + 原生 JS**：FastAPI 直接 `StaticFiles` 挂出去。

好处很直接：

- **镜像体积**：整镜像约 176MB，其中 Python 运行时 + 依赖占大头，前端几乎不占空间
- **零构建**：改前端不用 npm install、不用 webpack，改完刷新就生效，迭代极快
- **无构建产物泄漏**：前端里没有 sourcemap

代价是代码量上去之后维护会变难。这个取舍在个人项目上是划算的，不一定适合团队项目。

### 二、镜像更新检测：别只看 tag

这是我觉得最容易踩坑的地方。

很多面板判断「有没有新版本」的逻辑是：拉一下远端 manifest，看 tag 指向的 digest 和本地一样不一样。但问题是——**同一个 tag 背后的镜像可以被重新推送**。上游重新构建并 push `latest`，tag 没变，但镜像内容变了。如果你只看 tag 或只看本地 image ID，就会永远提示「已是最新」，再也收不到更新。

正确的做法是**比对 registry 返回的 manifest digest 与本地 image 的 `RepoDigests`**：

```python
remote = client.images.get_registry_data(name).id      # 远端 manifest digest
local  = container.image.attrs.get("RepoDigests")       # 本地 RepoDigests 列表
# 有交集 → 无更新；无交集 → 有更新
```

我们后面加了 `OFFICIAL_ICON_VERSIONS` 那套「版本指纹」机制，思路是同一个：**用内容指纹判断是否过期，而不是用版本号字符串**。

### 三、三层拉取兜底：国内拉不动镜像的 reality

`client.images.pull()` 直接调，在国内经常要么超时要么 TLS handshake 失败。我们做了三层 fallback：

1. **加速源** —— 先试配置的 registry mirror
2. **dockerd 直连** —— 绕过加速源的原生 pull
3. **容器内代理 `docker load`** —— 最后在面板容器内部通过 `HTTP_PROXY` 拉 tar 再 load 进去

关键是**每一层都要有超时**。最初 `client.images.pull()` 没有超时参数，一个 429 限流能把请求挂到天荒地老，页面一直转圈。现在每层都包了超时并且把失败原因透传到前端，用户能知道到底卡在哪一层。

### 四、digest 比对 + 三层拉取组合起来才完整

单有 digest 比对不够——你检测出有更新，拉不下来还是白搭；单有拉取兜底也不够——你不知道该不该更新。

我们的完整链路：

```
detect(digest) → 决定是否需要更新
     ↓
pull(加速源) → pull(dockerd) → load(容器内代理)
     ↓
重建容器（保留原 volumes / env / ports / restart policy）
```

重建容器时最容易丢的是**用户手工加的配置**（比如后来手工 `docker update` 过的资源限制）。我们把原容器的 `Config` 和 `HostConfig` 序列化后回灌，避免「更新一次配置全丢」这种常见事故。

### 五、远程节点的两种通道

远程节点支持两条路：

- **HTTP Agent 模式**：目标机跑同镜像，`MODE=agent`，用 `AGENT_TOKEN` 认证。好处是能拿完整 API 能力，坏处是要装东西
- **纯 SSH 通道**：通过 SSH 执行命令解析输出，不装任何东西。适合「只想远程看状态、改个 compose」

实际用下来，SSH 通道的兼容性更好——很多机器上装个 Python 环境都费劲。HTTP Agent 适合要完整功能的场景。

### 六、安全模型必须写清楚

面板挂载了 `docker.sock`。**能操作 Docker daemon，基本就等于有宿主机的 root 权限**。所以：

- 默认不做 Basic 认证（方便内网自用），但**必须**支持 `ADMIN_USER` / `ADMIN_PASSWORD` 环境变量
- 文档里明确写了「不要直接暴露公网」
- 文件管理通过 `FS_ROOTS` 限定可访问的根目录，避免面板变成任意文件读取器

这不是"以后再说"的问题，是这类面板第一天就要定的边界。

---

### 七、部署

```bash
docker run -d --name docker-link --restart always -p 9080:9080 \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /opt/compose:/opt/compose:rw \
  -v docker-link-data:/data \
  -v docker-link-config:/app/config \
  -e TZ=Asia/Shanghai \
  lichangzhou/docker-link:latest
```

Agent 模式：

```bash
docker run -d --name docker-link-agent --restart always \
  -e MODE=agent \
  -e AGENT_TOKEN=your-secret-token \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  lichangzhou/docker-link:latest
```

### 附：这三个项目是怎么串起来的

单独用任何一个都能跑，但串起来才是一条完整的链路：

```
Docker-Link（把容器跑起来 / 监控告警）
     ↓
EvoLink（把媒体库理清楚：缺口比对、归档刮削、13 维权重洗板、302 换链）
     ↓
Playable（Android / Windows / iOS 播放端，直接播 .strm，自动跟随 302）
```

分工上有个细节值得一提：**EvoLink 生成的 `.strm` 里写的是签名直链，用 HTTP 302 跳转到真实地址**，所以链接是有时效的。播放端必须跟随 302，不能把 `.strm` 里的 URL 当成最终地址缓存下来——否则过期后播不出来。Playable 就是按这个前提实现的。

三个仓库：Docker-Link（本篇）、EvoLink（https://github.com/lichangzhou/Evolink）、Playable（https://github.com/lichangzhou/Playable）。

### 开源

项目完全开源（MIT），代码在这里：

- GitHub：https://github.com/lichangzhou/Docker-link
- Docker Hub：https://hub.docker.com/r/lichangzhou/docker-link

目前只发布了 `linux/amd64` 架构镜像，ARM 平台需要自行构建。功能需求和问题都欢迎在 GitHub 上提 issue。
