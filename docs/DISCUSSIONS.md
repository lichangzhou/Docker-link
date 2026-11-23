# Discussions · 问题反馈

欢迎在这里提问、讨论、提建议。**提 Issue 前请先搜一下已有讨论**，避免重复。

## 提问前请附上

1. Docker-Link 版本（`docker exec docker-link cat /app/version` 或面板右下角）
2. 部署方式（compose / docker run / Agent 模式）
3. Docker 版本（`docker version`）
4. 相关日志片段（`docker logs --tail 100 docker-link`）
5. 复现步骤

## 常见问题

<details>
<summary><b>面板打不开 / 502</b></summary>

确认容器为 healthy：`docker ps | grep docker-link`，端口映射为 `9080:9080`。
</details>

<details>
<summary><b>容器更新卡在"拉取镜像"</b></summary>

宿主 dockerd 的 `daemon.json` 未配 `proxies` 时，直连 registry 会超时。
Docker-Link 已内置三层兜底（加速源 → dockerd 直连 → 容器内代理 `docker load`），
请在「设置 → 网络与镜像加速」里配置代理，并确保 `NO_PROXY` 包含内网地址。
</details>

<details>
<summary><b>更新后仍显示"有更新"</b></summary>

加速源返回的是 amd64 子清单 digest，比对需统一用索引 digest。
本地构建镜像（无 `RepoDigests`）显示 `unknown` 属正常现象。
</details>

<details>
<summary><b>远程节点连不上</b></summary>

- Agent 端须设置 `AGENT_TOKEN`，面板端填的令牌必须完全一致
- 令牌通过请求头 `X-Agent-Token` 传递
- 内网直连填 `http://<节点IP>:9081`；公网建议走 SSH 隧道
</details>

<details>
<summary><b>Compose 项目读取 404</b></summary>

`COMPOSE_DIR` 必须与 bind 挂载路径完全一致（都指向同一个目录）。
</details>

## 安全提醒

**请勿在公开 Issue / Discussion 中粘贴 Token、密码、代理凭据或内网地址。**
