# my-sso

本项目是一个最小化的 SSO 登录示例（Vue 3 + Element Plus 前端 + 简易 Express 后端）。

功能：
- 账号密码登录（示例后端，会对任意非空账号/密码返回 token）
- 支持记住密码（localStorage）
- 登录成功后，如果 URL 中存在 `redirect` 参数，会带上 `token` 跳回原地址
- 使用 Element Plus 组件，兼容移动端样式

快速启动：

1. 安装依赖（Node 版本建议 v24.13.0）：

```bash
npm install
```

2. 启动开发环境（同时启动后端和 Vite）：

```bash
npm run dev
```

3. 访问：
- 前端默认地址： http://localhost:5173
- 后端默认地址： http://localhost:4000

示例：访问登录并返回：

```
http://localhost:5173/login?redirect=https://example.com/welcome
```

登录后会跳回 `https://example.com/welcome?token=...`

说明：
- 这是示例项目，生产环境请替换为安全的认证逻辑（密码哈希、HTTPS、CSRF、令牌签名/JWT 等）。
