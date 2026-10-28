# 素材归档 · NAS

把照片、视频和文档按设备、格式、年月整理到归档目录。先扫描预览，再手动复制；保留素材原件，并按内容去重。

这是使用教程和安装包发布仓库，应用源码不在此仓库公开。

## 下载与安装

**当前版本：1.3.1 · 2026-10-28**　[查看最新版本与全部下载](https://github.com/superdaxigua/material-archiver/releases/latest)

| 使用方式 | 安装入口 | 完整教程 |
|---|---|---|
| 飞牛应用中心 | [下载 FPK 1.3.1](https://github.com/superdaxigua/material-archiver/releases/download/v1.3.1/material-archiver-1.3.1.fpk) | [飞牛安装与使用](docs/fnos.md) |
| Docker / Compose | 镜像 `superdaxigua/material-archiver:1.3.1` | [Docker 安装与使用](docs/docker.md) |
| NAS 图形界面部署 | [下载 Compose 配置](https://github.com/superdaxigua/material-archiver/releases/download/v1.3.1/docker-compose.yml) | [Docker 教程中的图形界面配置](docs/docker.md#使用-nas-图形界面) |

镜像地址：[Docker Hub](https://hub.docker.com/r/superdaxigua/material-archiver) · [GHCR](https://github.com/users/superdaxigua/packages/container/package/material-archiver)。均可免登录拉取，提供 `1.3.1`、`1.3`、`latest` 标签；日常部署建议固定完整版本。

## 能做什么

- 按设备、文件类型和日期整理图片、视频、文档及其他文件。
- 支持设备名称映射，分类字段可调整、排序。
- 关联 XMP 随照片归档；确认配对的 Live Photo 照片与视频共用照片目录和主文件名。
- 完整预览按素材组分页，展示关联依据、目标路径和待处理原因。
- 预览突出源文件、预计复制容量、重复副本和待处理组；详细统计可展开查看。
- 三步新建归档、独立任务记录，适配桌面与手机。
- 根据内容去重；同名不同内容保留为不同副本。
- 查看任务进度，取消、继续、重试失败项。
- 使用应用独立账号登录，支持忘记账号后的管理员恢复。

例如：

```text
归档目录/
├── 图片/相机名称/非RAW/2026/10/photo.jpg
├── 视频/设备名称/2026/10/clip.mov
├── 文档/PDF/2026/10/report.pdf
└── 其他/ZIP/archive.zip
```

实际路径取决于元数据与所选规则。缺少拍摄日期时归入“日期未知”，缺少设备时显示“未知设备”；归档年月固定按北京时间 UTC+8 计算。

## 第一次使用

1. 按自己的部署方式安装，并准备独立的测试输出目录。
2. 打开 `http://NAS地址:8765`，首次自行创建用户名和密码；密码至少 8 个字符。
3. 在“新建归档”的“选择目录”步骤填写源目录和输出目录，点击“下一步：设置规则”。Docker 使用容器内路径；飞牛选择已授权目录。
4. 默认规则可直接使用；展开分类调整字段和顺序，按需填写设备名称映射，然后点击“扫描预览”。
5. 在“确认预览”查看统计、展开素材组、翻页或筛选待处理项，确认后点击“开始归档”。返回修改后需重新扫描。
6. 在“任务记录”打开任务查看进度、成员结果或继续／重试。浏览历史不会覆盖当前页面中的新建草稿，切换页面不会取消后台任务。

从 1.3.0 升级到 1.3.1 无需数据迁移；保留原有配置、账号、目录授权和任务数据。尚未扫描的新建草稿仅保留在当前页面会话，刷新页面不会保存草稿。

建议先复制少量测试素材确认路径与权限。应用执行复制归档，不移动、不删除原件；归档副本不能替代独立备份。输出位置需要支持硬链接，例如本地 ext4/Btrfs。

## 平台与验证范围

| 环境 | 状态 |
|---|---|
| Docker linux/amd64（Intel/AMD 64 位） | 1.3.1 原生 CI 验证；历史 1.2.0 有飞牛 NAS 实机记录 |
| Docker linux/arm64（ARM 64 位） | 原生 ARM CI 通过；未做 ARM NAS 实机验收 |
| 飞牛 FPK | 需要 fnOS 1.2.0401+、Python 3.12 应用依赖；1.3.1 尚未实机升级，历史版本有 x86_64 实机记录；ARM 未验证 |
| 群晖、威联通、Unraid、TrueNAS SCALE | 可按 Docker/Compose 方式部署，理论支持，未逐平台实机验证 |

容器不支持 i386、armv7。Docker Hub/GHCR 的可达性取决于网络；历史 1.2.0 的 NAS 直连仓库曾超时，经开发机中转完成镜像验收；不代表 1.3.1 已完成 NAS 实机验收。

## 更多说明

- [Docker：挂载、权限、更新和恢复](docs/docker.md)
- [飞牛：下载、安装、授权和恢复](docs/fnos.md)
- [照片、XMP 与 Live Photo 的整理规则](docs/associated-resources.md)
- [从旧版升级到 1.3.1](docs/associated-resources.md#从旧版本升级)
- [常见问题](docs/faq.md)
- [版本更新记录](CHANGELOG.md)
- [发布文件校验](docs/downloads.md)

遇到问题可在 [Issues](https://github.com/superdaxigua/material-archiver/issues) 反馈，附版本、部署方式、复现步骤及脱敏后的报错。请勿上传账号密码、Cookie、令牌或真实私密素材。

## 问题反馈与功能建议

遇到问题或有改进建议，请登录 GitHub 后打开[反馈入口](https://github.com/superdaxigua/material-archiver/issues/new/choose)，选择「Bug 反馈」或「功能建议」中文表单。提交前请先[搜索已有反馈](https://github.com/superdaxigua/material-archiver/issues)，相同问题可直接补充信息，处理进度也会记录在那里。

报告 Bug 时，请填写软件版本、安装方式、运行环境、复现步骤、预期结果和实际结果，可附报错截图、相关日志或失败记录。Docker 用户请补充镜像标签（使用 latest 时附镜像摘要或拉取日期）及挂载配置。公开提交前请删除密码、Cookie、令牌、个人路径和文件名等敏感信息，无需上传原始素材。

