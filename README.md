# @char46/cycletls-linux-musl-x64

[cycletls](https://github.com/Danny-Dasilva/CycleTLS) Go 服务端二进制的 **linux x64 静态（musl 兼容）构建**，供 Alpine/musl 环境使用。

## 为什么存在

npm 的 `cycletls` 包自带的 linux 二进制是 **glibc 动态链接**（`PT_INTERP=/lib64/ld-linux-x86-64.so.2`），在 Alpine/musl 容器中 exec 会报 `ENOENT`。本包用 `CGO_ENABLED=0` 重新构建上游同一 commit，得到**完全静态**的二进制，glibc/musl 通吃。

## 用法

本包只含二进制，JS 侧仍用 npm 的 `cycletls`（必须版本对应）。通过 `executablePath` 注入：

```js
const initCycleTLS = require('cycletls').default
const binPath = require.resolve('@char46/cycletls-linux-musl-x64/index')
const cycletls = await initCycleTLS({ executablePath: binPath })
```

## 版本对应（重要）

二进制与 JS 侧通过私有 WebSocket 协议通信，**必须同源构建**：

| 本包版本 | 上游 commit | 对应 npm cycletls |
|---|---|---|
| 2.0.5 | [`a96895f4`](https://github.com/Danny-Dasilva/CycleTLS/commit/a96895f4) | 2.0.5 |

完整对应关系见 GitHub Actions workflow 的输入记录。

## 许可

上游为 **GPL-3.0**，本包为同一源码的再分发，同样以 GPL-3.0 发布（见 LICENSE）。
源码：https://github.com/Danny-Dasilva/CycleTLS（按上表 commit 构建，构建命令见
[workflow](.github/workflows/build-musl.yml)：`CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w"`）。
