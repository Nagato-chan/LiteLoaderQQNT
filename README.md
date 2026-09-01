# LiteLoaderQQNT（兼容修复 fork）

本仓库是 [LiteLoaderQQNT](https://github.com/LiteLoaderQQNT/LiteLoaderQQNT)（MIT，上游已于 2026-05 归档）的 fork，**仅包含一处兼容性修复**，不新增任何功能。

## 与上游的唯一差异

- `src/main/store.js` `scanPluginDirectory`：`path.join(dirent.path, …)` → `path.join(dirent.parentPath ?? dirent.path, …)`（1 行）

### 为什么

`dirent.path` 自 Node.js v21.5.0 起标记弃用、v23.0.0 起触发运行时告警、**v24.0.0 起正式移除**（Node.js v20 起它已是 `dirent.parentPath` 的别名，官方推荐直接使用 `dirent.parentPath`）。QQ 9.9.32（2026-03 重编版）自带的 Electron/Node.js 不再提供该属性，取值 `undefined` 会让 `path.join` 抛出 `TypeError: The "path" argument must be of type string`，主进程弹 Error 对话框、QQ 停在启动阶段。

改用 `dirent.parentPath ?? dirent.path` 后新旧 Node.js 双向兼容：

- QQ 9.9.32+（新 Node.js）：走 `dirent.parentPath`，正常启动
- QQ 9.9.25 ~ 9.9.31（旧 Node.js）：走回退的 `dirent.path`，行为不变

已在 9.9.25 / 9.9.31 / 9.9.32 沙盒实测，9.9.32 应用后启动到登录窗。

## 使用

与上游一致，任选其一：

1. 直接使用本仓库：`git clone https://github.com/Nagato-chan/LiteLoaderQQNT.git`，按 LiteLoaderQQNT 官方安装文档（<https://liteloaderqqnt.github.io>）配置；
2. 已有 LiteLoader 安装：仅将 `src/main/store.js` 中 `scanPluginDirectory` 的 `dirent.path` 一行改为 `dirent.parentPath ?? dirent.path` 即可，无需替换整个仓库。

## 许可证

[MIT](LICENSE)，fork 自 [LiteLoaderQQNT/LiteLoaderQQNT](https://github.com/LiteLoaderQQNT/LiteLoaderQQNT)，版权归上游作者。
