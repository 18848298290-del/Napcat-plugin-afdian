# AfdianNap - NapCat 爱发电插件

一个 [NapCat](https://napneko.github.io/) 插件，用于将 [爱发电](https://afdian.com/) 平台与 QQ 打通，提供赞助通知、订单查询、入群验证等功能。

## ✨ 功能特性

### 🔔 Webhook 实时通知
- 接收爱发电 Webhook 回调，实时推送赞助消息到指定 QQ 群 / 私聊
- 支持 Webhook 签名验证，防止伪造请求
- 自动订单去重，避免重复通知（最多缓存 5000 条订单号）

### 📋 QQ 命令系统
| 命令 | 说明 | 权限 |
|------|------|------|
| `/afdian 发电` | 显示爱发电主页链接 | 所有人 |
| `/afdian 赞助列表` | 列出所有赞助者 | 所有人 |
| `/afdian help` | 显示帮助菜单 | 所有人 |
| `/afdian status` | 查看插件运行状态 | 管理员 |
| `/afdian test` | 发送测试通知 | 管理员 |
| `/afdian history [N]` | 查看最近 N 条通知记录 | 管理员 |
| `/afdian order <订单号>` | 查询指定订单 | 管理员 |
| `/afdian orders [页码] [数量]` | 分页查询订单列表 | 管理员 |

### ✅ 入群验证
- 自动验证新成员入群请求，通过爱发电订单备注中的 QQ 号进行匹配
- 可设置最低赞助金额门槛
- 验证失败自动通知管理员

### 🖥️ Web 管理面板
- 可视化配置管理
- 实时状态监控（30 秒自动刷新）
- 订单查询与 API 测试
- 通知历史记录查看
- 支持亮色/暗色主题

## 📦 安装

将本项目放入 NapCat 的插件目录中：

```bash
cd napcat/plugins
git clone https://github.com/18848298290-del/Napcat-plugin-afdian/blob/main/napcat-plugin-afdian.zip
```

确保目录结构如下：

```
plugins/
└── AfdianNap/
    ├── index.mjs
    ├── package.json
    └── webui/
        └── dashboard.html
```

## ⚙️ 配置

### 基础配置

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `enabled` | boolean | `true` | 启用/禁用插件 |
| `webhookPath` | string | `/afdian/webhook` | Webhook 回调路径 |
| `afdianUserId` | string | `""` | 爱发电用户 ID |
| `afdianToken` | string | `""` | 爱发电 API Token |
| `afdianApiBaseUrl` | string | `https://afdian.com/api/open` | 爱发电 API 地址 |
| `afdianHomeUrl` | string | `""` | 爱发电主页链接 |

### 通知配置

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `notifyGroups` | array | `[]` | 接收通知的 QQ 群号列表 |
| `notifyUsers` | array | `[]` | 接收通知的 QQ 号列表 |
| `messageTemplate` | string | (见下方) | 通知消息模板 |
| `dedupeEnabled` | boolean | `true` | 启用订单去重 |
| `historyLimit` | number | `100` | 最大历史记录数（1-1000） |
| `debugLog` | boolean | `false` | 启用调试日志 |

### 命令配置

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `commandEnabled` | boolean | `true` | 启用 QQ 命令 |
| `commandPrefix` | string | `/afdian` | 命令前缀 |
| `commandAdmins` | array | `[]` | 管理员 QQ 号列表 |

### 入群验证配置

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `joinVerifyEnabled` | boolean | `false` | 启用入群验证 |
| `joinVerifyGroups` | array | `[]` | 需要验证的群号列表 |
| `joinVerifyPages` | number | `5` | 查询订单页数 |
| `joinVerifyMinAmount` | number | `0` | 最低赞助金额（元） |
| `joinVerifyAdminGroups` | array | `[]` | 验证失败通知的群号 |
| `joinVerifyAdminUsers` | array | `[]` | 验证失败通知的 QQ 号 |

### 安全配置

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `verifySignature` | boolean | `false` | 启用 Webhook 签名验证 |

### 消息模板变量

通知消息支持以下模板变量：

| 变量 | 说明 |
|------|------|
| `{{userName}}` | 赞助者昵称 |
| `{{planTitle}}` | 赞助方案标题 |
| `{{amount}}` | 赞助金额 |
| `{{outTradeNo}}` | 订单号 |
| `{{time}}` | 赞助时间 |
| `{{remark}}` | 订单备注 |

## 🚀 使用方法

1. 在爱发电后台设置 Webhook 回调地址：`http://<your-server>:<port>/afdian/webhook`
2. 在插件配置中填写爱发电用户 ID 和 API Token
3. 配置接收通知的 QQ 群号或 QQ 号
4. 如需使用入群验证功能，开启 `joinVerifyEnabled` 并配置相关群号

## 🌐 API 接口

Web 管理面板提供以下 API 接口（需 Bearer Token 认证）：

| 方法 | 路径 | 说明 |
|------|------|------|
| `GET` | `/api/Plugin/ext/AfdianNap/status` | 插件状态与统计 |
| `GET` | `/api/Plugin/ext/AfdianNap/config` | 获取配置 |
| `POST` | `/api/Plugin/ext/AfdianNap/config` | 保存配置 |
| `GET` | `/api/Plugin/ext/AfdianNap/history` | 通知历史记录 |
| `GET` | `/api/Plugin/ext/AfdianNap/orders` | 查询订单列表 |
| `POST` | `/api/Plugin/ext/AfdianNap/query-order` | 查询指定订单 |
| `POST` | `/api/Plugin/ext/AfdianNap/test-api` | 测试爱发电 API 连通性 |
| `POST` | `/api/Plugin/ext/AfdianNap/test-notify` | 发送测试通知 |
| `POST` | `/api/Plugin/ext/AfdianNap/clear-history` | 清空历史记录和去重缓存 |
| `GET` | `/api/Plugin/ext/AfdianNap/health` | 健康检查（无需认证） |

## 🔒 安全说明

- Token 在 UI 和日志中均以 `****` 脱敏显示
- Webhook 签名验证支持多种算法兼容
- Web 管理面板需 Bearer Token 认证
- 敏感字段在日志中自动过滤

## 🛠️ 技术栈

- **运行时**：Node.js (ES Module)
- **依赖**：`napcat-types` 0.0.16
- **无外部 HTTP 库**：使用 NapCat 内置路由系统
- **存储**：内存存储（重启后历史记录和去重缓存清空）

## 💜 支持开发者

如果你觉得这个插件对你有帮助，欢迎去爱发电支持一下：
https://www.ifdian.net/a/FengLan1201

## 📄 许可证

MIT License

## 🙏 致谢

- [NapCat](https://napneko.github.io/) - QQ Bot 框架
- [爱发电](https://afdian.com/) - 创作者赞助平台
