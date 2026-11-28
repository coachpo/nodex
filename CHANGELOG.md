# 变更记录

版本标签从 `v0.0.1` 开始，该版本包含此前的全部提交。更早的分节以提交定位历史变更。

## v0.0.4

- Workspace 镜像 `ghcr.io/coachpo/cloudex-workspace` 随 Cloudex 发版，与 APP 镜像使用相同的发布标签，不再按 Codex 版本定时发布 `codex-<版本>` 标签。预构建的 `cloudex-api` 默认引用同一提交的 `cloudex-workspace:sha-<完整提交>`，使用前在各 Host 拉取该镜像。[预构建镜像](docs/operations.md#预构建镜像) · [默认工作镜像](docs/operations.md#默认工作镜像)
- 从源码用 Compose 构建时，`CLOUDEX_WORKSPACE_IMAGE` 同时决定工作镜像的构建标签和 API 内置的默认镜像，默认值改为 `cloudex/workspace:local`。升级后先重新构建工作镜像，已有 Workspace 继续使用创建时的镜像引用。[默认工作镜像](docs/operations.md#默认工作镜像)

## v0.0.3

- 同一轮工具往返中触发自动压缩后，Codex 在新上下文窗口的续写可经 WS 和 HTTP/SSE 正常完成，不再收到 `409 submission_outcome_unknown`。[流式转发](docs/codex-gateway.md#流式转发)
- Workspace 镜像 `ghcr.io/coachpo/cloudex-workspace` 以 `linux/amd64` 与 `linux/arm64` 多架构发布，两种架构分别通过版本核对和网关验收后才更新标签。原单架构的 `codex-0.172.0` 已按多架构重新发布，其中的启动器在启动 Codex 前读取控制面保存的启动配置，须搭配 v0.0.2 或更新的 APP。GHCR 不再提供 v0.0.1 的 APP 镜像和原单架构镜像。[预构建镜像](docs/operations.md#预构建镜像)

## v0.0.2

- 管理员可在设置页统一编辑 Codex 的 `config.toml` 和 `AGENTS.md`。绑定授权的 Workspace 在下一次启动 Codex 时读取保存的版本，Desktop 重新连接后生效。[Codex 启动配置](docs/operations.md#配置-codex-启动文件)
- 新建 Workspace 时可使用随机名称或掷骰子换一个，SSH 别名优先使用可读的 Workspace 名称，Codex 设备码支持一键复制和打开验证页时复制。

## v0.0.1

- 首个版本标签。APP 镜像 `ghcr.io/coachpo/cloudex-api`、`cloudex-agent` 和 `cloudex-web` 自此按发布标签以 `linux/amd64` 与 `linux/arm64` 多架构发布。[预构建镜像](docs/operations.md#预构建镜像)

### Workspace 工作流

- Workspace 可绑定独立的 Sign in with Codex 或 Sign in with ChatGPT 授权。Codex 网关优先走 Responses WebSocket，并在生成未提交的条件下保留 HTTP/SSE 回退。[Codex 网关](docs/operations.md#codex-网关)
- **Hosts → Add host** 支持 SSH 直连。管理员核对主机指纹后，可使用密码或私钥接入远端 Docker，直接在该 Host 放置和管理 Workspace。[加入远程 Host](docs/operations.md#加入远程-host)
- 管理员可移除远程 Host 的登记，再用原名称重新登记。远端 Docker 资源继续保留。[移除远程 Host](docs/operations.md#移除远程-host)
- 控制台和 API 已移除 Cloud Task、全局模型服务和 Codex 沙箱设置。通过 SSH、Web Terminal 或 Codex Desktop 使用 Workspace，Git 页可查看工作树快照。
- 可为每个 Workspace 保存 HTTPS 仓库凭据。首次 clone 和后续 fetch、push 可复用令牌，控制台只回显配置状态和用户名。[仓库凭据](docs/workspace-lifecycle.md#仓库凭据)
- 管理员在设置页统一管理 SSH 公钥，运行中的 Workspace 无需重建即可接收新公钥。Host 调度提供 Enabled、Cordon 和 Disabled。[SSH 连接](docs/operations.md#通过-ssh-连接-workspace)
- Workspace 的授权绑定跨容器重建保留。运行实例从控制面获得独立的网关凭据。工作树和 SSH 主机身份使用独立卷。容器替换从镜像初始化 home。[数据保留](docs/operations.md#数据保留)
- 同一容器切回同一份授权的同一代次后，可恢复原 Codex 会话并续聊。[恢复授权与凭据](docs/operations.md#恢复授权与凭据)

### 管理员账号与界面语言

- 首次从控制台创建管理员，重启后沿用原账号。更改用户名或密码后当前浏览器继续登录，其他会话失效。丢失凭据可用 `reset-admin` 重新初始化。[账号找回](docs/operations.md#管理员账号)
- 控制台按浏览器语言显示简体中文或英文，包括错误、日期和数字格式。主题选择可跨访问保留。
- 可在控制台设置 SSH 主机名、Agent 公开地址、各 Host 的磁盘预留容量和操作并发上限。这些更改无需重启，磁盘容量改由主机详情页设置，原 `CCC_AGENT_DISK_BYTES` 配置已移除。[Host 容量](docs/operations.md#主机磁盘预留容量)
- 默认工作镜像可在设置页切换，已有 Workspace 沿用创建时的镜像。
- 当时的后台任务支持设置 Codex 沙箱及使用 API 密钥的自定义 Responses API 服务，可选模型和请求头，凭据不回显。未指定服务时沿用 OpenAI。这些能力随后随 Cloud Task 移除。
- 产品更名为 Cloudex，镜像、配置变量前缀（`CCC_` → `CLOUDEX_`）和 SSH 别名随之更名。旧数据、旧版 Agent 和逐 Workspace 发布的 SSH 端口停止受支持。

## 多 Host 管理 — `e05e54e`、`8cb5bee`、`1b97801`、`af7b3df`

- 可统一注册、盘点本地与远程 Docker Host，创建 Workspace 时手动选址并预留 CPU、内存和磁盘。既有 Workspace 归入本机 Host，保留原代次、卷、操作历史和 SSH 身份。
- 生命周期操作和当时的后台任务在归属 Host 执行。慢 Host 不会阻塞其他 Host 的状态更新。Agent 证书自动续期，已建立的 SSH、终端及任务连接继续使用。
- Agent 已连接但 Docker 不可用时，Host 显示为 `degraded`，实时视图和新任务按离线处理。

## 生命周期与 Cloud Task 恢复 — `d6f3587`、`f6ed40c`、`dee3550`

- 后台任务可显示收尾状态，中断后可恢复已记录的结果，确认启动被拒绝的任务可重新排队。
- Start 在发出前失败或 Stop 失败时，操作历史显示失败，Workspace 继续显示实际状态。
- Web Terminal 支持粘贴大段文字。

## SSH Gateway 与 Cloud Task 观测 — `2e5ad61`

- Workspace 改用共享 SSH Gateway 并保留独立主机密钥。既有公钥与历史保留。V1.1 直连容器须重建并更新客户端 SSH 配置后切换。
- 当时的 Cloud Task 支持按 Workspace 和状态筛选、查看计数及线程、Turn、事件和用量快照，观测范围仅限后台任务。

## V1.1：Codex Desktop 直连 SSH — `998c29b`

- Workspace 支持公钥管理、独立 SSH 端口和 Codex Desktop 导入，重建后保留 SSH 主机身份。Access 页可复制 OpenSSH 配置和主机公钥。

## 初始单机版 — `650cfa5`

- 支持通过 Docker Compose 运行，提供 Workspace 生命周期、Web Terminal、Codex 后台任务、日志回放和 Git 快照。
- 支持 OpenAI API Key 和 Codex CLI 账号认证。
