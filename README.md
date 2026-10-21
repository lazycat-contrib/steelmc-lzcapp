# SteelMC for LazyCat

将 [SteelMC](https://github.com/Steel-Foundation/SteelMC) 打包为 LazyCat LPK v2。SteelMC 是一个用 Rust 编写的 Minecraft Java 版服务器，目标是匹配原版行为并更好地利用现代多核硬件。

> SteelMC 目前仍是 pre-alpha：世界生成已相当完整（区块生成与原版逐方块一致），可以连接、移动、交互、保存区块，但生存玩法、实体行为、插件生态尚不完整。**不建议用它替换正式服务器。**

## 连接方式

| 客户端 | 协议 | 端口 |
| --- | --- | ---: |
| Minecraft Java Edition | TCP | `25565` |

安装后打开应用可查看当前主机名并一键复制服务器地址。SteelMC 没有 Web 管理后台，应用入口是内嵌的本地介绍页。

## 数据与运行方式

- 上游镜像：`ghcr.io/steel-foundation/steelmc:v0.16.0-mc26.3`
- 运行镜像：`ghcr.1ms.run/steel-foundation/steelmc:v0.16.0-mc26.3`
- 工作目录：容器内 `/steel`（镜像默认工作目录）
- 持久化：
  - `/lzcapp/var/steelmc/config` → `/config`（`config.toml`、`worlds.toml`、`favicon.png`）
  - `/lzcapp/var/steelmc/saves` → `/saves`（世界存档）
  - `/lzcapp/var/steelmc/logs` → `/.logs`（日志）
- 服务名：`steel`
- 未添加健康检查：镜像基于 `scratch`，不含 shell 或探测工具，与上游 Compose 保持一致

默认 `online_mode = true`，需要正版账号登录。若要离线游玩，修改 `config/config.toml` 中的 `online_mode` 与 `encryption` 后重启应用。

## 本地构建

```bash
lzc-cli project build -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

## 自动发布

`.github/workflows/lazycat.yml` 使用 `ca-x/lazycat-github-action` 检查新的上游镜像标签（`v*.*.*-mc*.*`），创建版本化 GitHub Release Asset，并只发布到喵喵私有商店。需要为仓库配置：

- `APPSTORE_URL`
- `APPSTORE_TOKEN`
- `APP_ID`，可选
- `PRIVATE_STORE_GROUP_CODES`，可选，用于私有分组

镜像使用 GHCR 默认镜像加速前缀，并在发布前校验 `linux/amd64` 源镜像与镜像加速地址的 digest 一致。
