# 下载与校验

[最新版本下载页](https://github.com/superdaxigua/material-archiver/releases/latest) 始终指向当前正式 Release。

| 文件 | 用途 |
|---|---|
| [material-archiver-1.3.1.fpk](https://github.com/superdaxigua/material-archiver/releases/download/v1.3.1/material-archiver-1.3.1.fpk) | 飞牛应用中心手动安装 |
| [docker-compose.yml](https://github.com/superdaxigua/material-archiver/releases/download/v1.3.1/docker-compose.yml) | Docker/Compose 部署示例，使用前修改路径与 UID/GID |
| [SHA256SUMS](https://github.com/superdaxigua/material-archiver/releases/download/v1.3.1/SHA256SUMS) | 校验上述两个文件 |
| GitHub 自动生成的 Source code 压缩包 | 仅包含此教程仓库的文档与配置，不是 FPK 安装包 |

下载后，将需要校验的文件与 SHA256SUMS 放在同一目录。单独核对所下载文件对应的摘要：

Linux：

```sh
sha256sum material-archiver-1.3.1.fpk
sha256sum docker-compose.yml
```

macOS：

```sh
shasum -a 256 material-archiver-1.3.1.fpk
shasum -a 256 docker-compose.yml
```

Windows PowerShell：

```powershell
Get-FileHash .\material-archiver-1.3.1.fpk -Algorithm SHA256
Get-FileHash .\docker-compose.yml -Algorithm SHA256
```

输出应与 SHA256SUMS 对应文件的值一致。不一致时重新下载，不要继续安装。

当前 FPK 由 1.3.1 正式发布流水线构建，未经重新打包；本教程仓库不包含应用开发代码或开发提交历史。FPK 与 Docker 镜像是可运行分发产物，内含运行代码，不代表技术上无法提取或查看。

[飞牛安装教程](fnos.md) · [Docker 部署教程](docker.md) · [返回首页](../README.md)

