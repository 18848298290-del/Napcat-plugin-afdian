# AfdianNap

NapCat 爱发电对接助手。用于接收爱发电 Webhook、查询订单、向 QQ 群/用户发送赞助通知，并可按爱发电订单备注自动校验入群申请。

开发者：FengLan ｜ 爱发电：https://www.ifdian.net/a/FengLan1201

## 功能

- 爱发电 Webhook 通知接收
- 爱发电 OpenAPI 订单查询
- QQ 群/私聊赞助通知
- WebUI 配置页与通知历史
- QQ 指令查询发电主页、赞助列表、订单、状态
- 入群申请发电校验：匹配订单备注中的 QQ 号并校验最低发电金额
- 通知历史与去重缓存持久化，NapCat 重启后仍可保留最近状态
- 爱发电 API 请求超时保护，避免接口异常时长期卡住 WebUI 或 QQ 指令
- Webhook 路径和 QQ 号配置规范化，减少生产环境误配置风险

## 安装

1. 将本目录放入 NapCat 原生插件目录。
2. 确认 `package.json` 中插件名为 `AfdianNap`，主入口为 `index.mjs`。
3. 重启 NapCat。
4. 在 NapCat 插件页面启用 AfdianNap。

## 配置

进入插件页面：

```text
/plugin/AfdianNap/page/dashboard
```

或在 NapCat WebUI 的插件扩展页面中打开 AfdianNap。

建议至少配置：

- `爱发电 user_id`
- `爱发电 token`
- `通知 QQ 群` 或 `通知 QQ 用户`
- `爱发电主页`

默认会开启签名校验。如果未配置爱发电 token，Webhook 会被拒绝处理。

Webhook 路径只能包含字母、数字、斜杠、下划线和短横线。QQ 号、群号配置会自动过滤非数字项。

## Webhook 地址

默认 Webhook 路径：

```text
/afdian/webhook
```

完整地址格式：

```text
https://你的公网域名/plugin/AfdianNap/api/afdian/webhook
```

如果 NapCat 运行在本地电脑，需要使用公网域名、反向代理或内网穿透，确保爱发电服务器可以访问。

修改 Webhook 路径后通常需要重启 NapCat，因为路由是在插件初始化时注册的。

## QQ 指令

默认指令前缀：

```text
/afdian
```

所有用户可用：

```text
/afdian 发电
/afdian 赞助列表
/afdian help
```

管理员可用，管理员 QQ 需要在配置里填写：

```text
/afdian status
/afdian test
/afdian history [数量]
/afdian order <订单号>
/afdian orders [页码] [数量]
```

## 入群发电校验

启用后，插件会处理指定群的入群申请：

1. 查询爱发电订单列表。
2. 从订单备注中提取 5-12 位数字作为 QQ 号。
3. 匹配申请入群的 QQ。
4. 如果金额达到配置的最低发电金额，则自动同意入群。
5. 未匹配、金额不足或缺少事件 flag 时，通知管理员群/用户人工处理。

需要配置：

- 启用入群发电校验
- 入群校验群号
- 入群校验查询页数
- 入群最低发电金额
- 入群校验通知群或通知用户

## 持久化状态

插件会在配置文件同目录生成运行状态文件：

```text
<配置文件名>.state.json
```

其中保存：

- 最近通知历史
- 已处理订单/事件去重缓存
- 状态更新时间

WebUI 中“清空历史/去重缓存”和“清空去重缓存”会同步更新该状态文件。

## 开发检查

运行语法检查：

```bash
npm run check
```

等价于：

```bash
node --check index.mjs
```
