# AIFit × Whoop

本地 Whoop OAuth 连接器，读取并展示：

- 基本资料
- 身高、体重、最大心率
- Recovery：恢复分数、HRV、静息心率、血氧、皮肤温度
- Physiological Cycle：Strain、平均/最大心率、消耗
- Sleep：睡眠表现、效率、呼吸率、睡眠阶段
- Workout：运动类型、Strain、心率、热量、距离

数据只从本机直接请求 Whoop API。服务默认绑定 `127.0.0.1`，令牌保存在
`.data/whoop-tokens.json`（权限 `600`），该目录已被 Git 忽略。

## 1. 创建 Whoop 应用

1. 打开 [Whoop Developer Dashboard](https://developer-dashboard.whoop.com/apps/create)。
2. 若首次使用，先创建 Team，再创建 App。
3. 勾选这些 scopes：
   - `read:profile`
   - `read:body_measurement`
   - `read:recovery`
   - `read:cycles`
   - `read:sleep`
   - `read:workout`
4. Redirect URI 先填写：

   ```text
   http://127.0.0.1:3000/auth/whoop/callback
   ```

   如果 Dashboard 拒绝 `http` 本地地址，先完成应用创建，再把本地服务通过
   HTTPS 隧道暴露，并把隧道地址加 `/auth/whoop/callback` 注册为 Redirect URI。
   Redirect URI 必须逐字符一致。

   Whoop 官方 OAuth 文档展示的 Redirect URI 示例使用 `https://` 或自定义
   scheme。若开发者门户不接受本地 `http://`，常见做法是注册一个 HTTPS 隧道
   地址；临时隧道地址改变后，也要同步更新 Dashboard 和 `.env` 中的值。

## 2. 写入本机凭据

在项目目录运行：

```bash
npm run setup
```

脚本会要求输入 Client ID、Client Secret 和 Redirect URI。Client Secret 输入时
不会回显，也不会写入 shell 历史。配置保存于本机 `.env`（权限 `600`）。

不要把 Client Secret、访问令牌或完整 `.env` 内容粘贴到聊天、截图或提交到 Git。

## 3. 连接并读取

```bash
npm start
```

然后打开 <http://127.0.0.1:3000>，点击“连接我的 Whoop”，在 Whoop 官方页面
登录并同意授权。

可用本地接口：

- `GET /api/data?days=30`：最近 30 天原始健康数据
- `GET /api/export?days=90`：下载最近 90 天 JSON
- `GET /healthz`：本地服务状态

## 开发验证

```bash
npm test
```

接口依据：

- [Whoop OAuth 2.0](https://developer.whoop.com/docs/developing/oauth)
- [Whoop v2 OpenAPI](https://developer.whoop.com/api)

