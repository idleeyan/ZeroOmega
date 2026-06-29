# ZeroOmega - Proxy SwitchyOmega 3

ZeroOmega 是 [Proxy SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega) 的 Manifest V3 升级版本，兼容 Chrome/Edge/Brave 等基于 Chromium 的浏览器。

## 功能特性

- **智能代理切换**：根据 URL 模式自动选择代理规则
- **多种代理协议**：支持 HTTP/HTTPS/SOCKS4/SOCKS5 代理
- **条件规则**：灵活的场景模式（rule list）配置
- **WebDAV 同步**：通过 WebDAV 服务同步配置（支持坚果云、Nextcloud、群晖 NAS 等）
- **跨设备配置**：可选使用浏览器内置同步（chrome.storage.sync）在登录同一账号的设备间自动同步 WebDAV 凭据
- **条件规则列表**：支持在线规则列表自动更新

## 安装

### 从 Chrome Web Store 安装
（待发布）

### 从源码构建

```bash
# 克隆仓库
git clone https://github.com/idleeyan/ZeroOmega.git
cd ZeroOmega

# 安装依赖
npm install

# 构建扩展
npm run build

# 加载到浏览器
# 1. 打开 chrome://extensions/
# 2. 启用"开发者模式"
# 3. 点击"加载已解压的扩展程序"
# 4. 选择 dist/ 目录
```

## WebDAV 同步配置

ZeroOmega 支持通过 WebDAV 服务同步代理配置，替代原有的 GitHub Gist 同步方式。

### 支持的 WebDAV 服务

| 服务 | WebDAV URL 示例 | 备注 |
|------|-----------------|------|
| 坚果云 | `https://dav.jianguoyun.com/dav/ZeroOmega.json` | 需要使用**应用密码**（非登录密码） |
| Nextcloud | `https://your-nextcloud.com/remote.php/webdav/ZeroOmega.json` | |
| 群晖 NAS | `https://your-nas:5006/ZeroOmega.json` | 需启用 WebDAV Server 套件 |
| 自建 WebDAV | `https://your-server/dav/ZeroOmega.json` | 任意兼容 WebDAV 的服务器 |

### 配置步骤

1. 打开扩展选项页面
2. 进入 **导入/导出** 标签页
3. 在 **同步** 区域填写：
   - **WebDAV URL**：完整的 WebDAV 文件路径（如 `https://dav.jianguoyun.com/dav/ZeroOmega.json`）
   - **用户名**：WebDAV 服务用户名
   - **密码**：WebDAV 服务密码（坚果云需使用应用密码）
4. （可选）勾选 **使用浏览器内置同步增强**：将 WebDAV 凭据通过 `chrome.storage.sync` 同步到其他设备
5. 点击 **启用同步**

### URL 格式说明

- 填写完整文件路径：`https://dav.example.com/path/ZeroOmega.json`
- 或填写目录路径（以 `/` 结尾）：`https://dav.example.com/path/` → 自动追加 `ZeroOmega.json`

### 同步机制

- **版本检测**：通过 WebDAV `ETag` 检测远端配置变更（ETag 不可用时回退到 `Last-Modified`）
- **冲突处理**：当本地与远端配置不一致时，状态变为 `conflict`，需手动选择"强制下载"或"重置同步"
- **定时检查**：每 5 分钟自动检查远端变更

## 项目结构

```
ZeroOmega/
├── _locales/          # 多语言文案（en/zh_CN/zh_TW/cs/fa）
├── css/               # 样式文件
├── img/               # 图标资源
├── js/                # 核心 JavaScript 模块
│   ├── omega.js            # Angular 控制器
│   ├── background.js       # 后台 Service Worker
│   ├── omega_target.min.js # 核心逻辑（Options/OptionsSync）
│   └── omega_target_chromium_extension.min.js  # Chrome 平台适配（含 WebDAV 传输层）
├── lib/               # 第三方库（Angular/Bootstrap/Codemirror）
├── partials/          # HTML 局部模板
├── popup/             # 弹出窗口
├── manifest.json      # 扩展清单（Manifest V3）
└── options.html       # 选项页面
```

## 从 SwitchyOmega 迁移

1. 在 SwitchyOmega 选项中导出备份（`.bak` 文件）
2. 安装 ZeroOmega
3. 在 ZeroOmega 选项 → 导入/导出 → 恢复备份中导入 `.bak` 文件

## 常见问题

### WebDAV 同步失败？

- **坚果云**：必须使用**应用密码**，不是登录密码。在坚果云网页端 → 安全选项 → 应用密码中生成。
- **跨域问题**：确保 WebDAV 服务允许浏览器跨域请求（CORS），或使用扩展的 `<all_urls>` 权限绕过。
- **证书问题**：自签名证书的 HTTPS 服务可能在浏览器扩展中失败，建议使用有效证书或 HTTP。

### 为什么不支持 GitHub Gist 同步了？

Gist 同步依赖 GitHub Token，存在权限过度授权和 Token 泄露风险。WebDAV 是标准化的文件传输协议，支持多种自建/商用服务，更灵活且安全。

## 开源协议

本项目基于 [Proxy SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega) 修改，遵循 **GPL-3.0 License**。

## 致谢

- 原始项目：[Proxy SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega) by @FelisCatus
- Manifest V3 迁移参考：[ZeroOmega](https://github.com/yjl9903/ZeroOmega) by @yjl9903

## 贡献

欢迎提交 Issue 和 Pull Request！

---
