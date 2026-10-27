# Docker 安装与使用

支持 `linux/amd64` 和 `linux/arm64`。宿主机需要 Docker；使用下面命令时还需要 Docker Compose。容器内自带运行依赖，无需在 NAS 安装 Python 或 Perl。

## 1. 准备目录和身份

在 NAS 上创建三个独立目录：

| 目录 | 用途 | 容器内路径 |
|---|---|---|
| config | 账号、会话、任务记录；需要持久保留 | `/config` |
| source | 待整理素材；只读挂载 | `/media/source` |
| archive | 归档副本；需要可读写 | `/media/output` |

查询用于运行服务的 NAS 用户数字身份：

```sh
id 用户名
```

将输出中的 `uid` 填为 PUID、`gid` 填为 PGID。不要使用 0；默认值是 1000:1000，但你的 NAS 用户可能不同。该身份需要读取源目录、读写输出目录和穿越相应路径的权限，NAS ACL 也需要允许。不要直接对正式素材递归 chmod/chown。

## 2. 保存 Compose 配置

[下载 docker-compose.yml](https://github.com/superdaxigua/material-archiver/releases/latest/download/docker-compose.yml)，也可复制仓库根目录的同名文件。将它放入自己的部署目录，修改三个宿主绝对路径、PUID 和 PGID。

```yaml
services:
  archiver:
    image: superdaxigua/material-archiver:1.3.0
    ports:
      - "8765:8765"
    environment:
      PUID: "1000"
      PGID: "1000"
    volumes:
      - /path/on/nas/material-archiver/config:/config
      - /path/on/nas/source:/media/source:ro
      - /path/on/nas/archive:/media/output
    stop_grace_period: 30s
    restart: unless-stopped
```

冒号左边是宿主路径，右边是容器内路径。`ro` 让源素材只读；不要给输出或 config 加 `ro`。

如果 NAS 的 8765 已被飞牛 FPK 或其他服务占用，把映射改为 `8766:8765`，访问时使用 8766。只修改左边的宿主端口。

## 3. 拉取并启动

在 Compose 文件所在目录执行：

```sh
docker compose pull
docker compose up -d
docker compose ps
```

无需登录 Docker Hub。备用镜像为 `ghcr.io/superdaxigua/material-archiver:1.3.0`，可替换 Compose 的 `image`；两个仓库内容相同。若拉取超时，先确认 NAS 到对应仓库的网络连通性。

打开 `http://NAS地址:8765`（或你修改的端口），首次自行创建应用账号。容器状态应逐渐显示为 healthy；没有创建账号时也可以健康。

## 4. 扫描与归档

源目录选择 `/media/source`，输出目录选择 `/media/output`。网页看不到宿主的 `/vol1/...`、`/vol2/...` 等路径，应选择冒号右边的容器路径。

先调整规则、扫描并检查预览。预览不会复制素材；手动开始后才生成副本。再次扫描相同素材会按内容去重，不会覆盖同名但不同内容的既有文件。

1.3.0 的 XMP 与 Live Photo 按素材组规划；预览可翻页和筛选待处理项。扫描或复制时设置不可编辑，预览后修改设置会要求重新扫描。具体规则见[关联资源说明](associated-resources.md)。

新副本使用配置的 UID/GID；既有副本和输出目录不会被递归更改属主。输出不可写时，预览前会提示检查只读挂载、PUID/PGID 或 NAS 权限。

## 使用 NAS 图形界面

支持 Compose/项目的界面可以直接导入并调整上方 YAML，不需要下载源码。

| 平台 | 部署方式 |
|---|---|
| 飞牛 Docker | 使用 Compose 项目或手动创建容器，填写镜像、端口、三个目录映射和环境变量 |
| 群晖 | 支持容器的机型使用 Container Manager 项目/Compose，旧系统功能以设备实际支持为准 |
| 威联通 | 支持容器的机型使用 Container Station 的应用/Compose 功能 |
| Unraid | 使用 Docker 容器配置，填写镜像、PUID/PGID、三个挂载和端口 |
| TrueNAS SCALE | 具有 Docker/Compose 功能的版本使用自定义应用或 YAML 部署，并检查数据集 ACL |

手动创建容器时，镜像填 `superdaxigua/material-archiver:1.3.0`，容器 HTTP 端口为 8765，其余参数与 YAML 保持一致。不同版本界面名称可能不同；1.3.0 尚未逐平台实机验收；飞牛历史版本的实机结果不能替代本次新功能验证。

## 新增素材挂载

默认将 `/media` 下每个直接子目录列为一个可访问目录。需要更多位置时，新增类似 `/宿主/另一个目录:/media/extra:ro` 的挂载，再重建容器并重新打开目录选择器。

也可设置 `ARCHIVER_ROOTS` 为逗号分隔的容器绝对路径，例如 `/photos,/archive`，并实际挂载对应目录。设置后替代默认范围；环境变量不会自动挂载宿主文件夹。

## 更新版本与持久化

更新前先停止服务，备份宿主 config 目录；归档副本和源素材按自己的备份策略保留。

```sh
docker compose stop
# 此时备份你的宿主 config 目录。
```

将 Compose 的 `image` 改为新版的完整版本标签，再执行：

```sh
docker compose pull
docker compose up -d
docker compose ps
```

必须继续使用原来的 `/config` 和归档目录。固定 `1.3.0` 时，单纯 pull 不会自动切换到未来的其他版本；使用 latest 前也应先阅读更新记录。修改挂载和环境变量后，使用 `up -d` 重建应用配置。

从 1.2.0 升级后，原有单文件任务保留历史结果，不会自动认定已验证配套关系。请重新扫描源目录，检查新增 XMP 和 Live Photo 的关联计划；不要删除历史输出目录。

正常停止会清理当前未完成的临时副本；重启后可在原任务继续或重试。不要在复制过程中直接断电或强制结束；断电后的历史隐藏临时文件需要确认没有活动任务后再处理。

## 忘记账号

在原部署目录执行：

```sh
docker compose exec archiver archiver reset-account
```

也可以用实际容器名执行 `docker exec -it 容器名 archiver reset-account`。交互输入用户名、新密码和确认密码，密码不回显。恢复会撤销旧凭据和全部旧会话，保留任务、原件与副本。不要删除 config 来恢复账号。

## HTTPS 与常用检查

容器只提供 HTTP。需要 HTTPS 时由 NAS 反向代理处理 TLS，必须保留原始 Host；Nginx 示例为 `proxy_set_header Host $http_host;`。从最终 HTTPS 地址验证登录和归档写操作。

```sh
docker compose ps
docker compose logs --tail 100 archiver
```

本教程不要求开放公网端口。优先在可信局域网试用。[返回首页](../README.md)

