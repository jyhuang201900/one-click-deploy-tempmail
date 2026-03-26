# Cloudflare Temp Email One-Click Deploy

基于 [dreamhunter2333/cloudflare_temp_email](https://github.com/dreamhunter2333/cloudflare_temp_email) 的一键部署包装脚本，目标是把“下载源码、生成配置、创建 Cloudflare 资源、构建并部署”尽量收敛到一次执行里。

上游作者 GitHub：[@dreamhunter2333](https://github.com/dreamhunter2333)

## 功能概览

- 自动下载官方源码，或复用你本地已有的源码目录
- 自动生成后端 Worker、前端 Worker 和前端环境配置
- 自动创建 D1 和 KV，并把 ID 回写到本地配置
- 自动初始化数据库
- 自动构建前端并部署前后端 Worker
- 自动补齐前端请求头修复，减少部署后接口请求异常

## 运行前准备

- Cloudflare 账号
- 已托管到 Cloudflare 的域名
- `Node.js 20+`
- `PowerShell 7` (`pwsh`)
- 可访问 GitHub / npm / Cloudflare 的网络环境

推荐域名结构：

- 收件域名：`example.com`
- 前端域名：`mail.example.com`
- API 域名：`email-api.example.com`

## 仓库文件

- `deploy-one-click.ps1`：主部署脚本
- `one-click.config.minimal.jsonc`：最小配置样例
- `one-click.config.example.jsonc`：完整配置样例

本地运行时配置文件使用 `one-click.config.jsonc`，已默认加入 `.gitignore`。

## 快速开始

1. 复制 `one-click.config.minimal.jsonc` 为 `one-click.config.jsonc`
2. 按你的域名和密码修改配置
3. 先执行预检查
4. 确认输出无误后再正式部署

最小配置示例：

```jsonc
{
  // 项目名称，用来生成默认 Worker / D1 / KV 名称
  "projectName": "my-tempmail",

  // 收件主域名
  "mailDomain": "example.com",

  // 前端域名前缀，最终为 mail.example.com
  "frontendSubdomain": "mail",

  // API 域名前缀，最终为 email-api.example.com
  "apiSubdomain": "email-api",

  // 后台 /admin 密码
  "adminPassword": "change-me"
}
```

预检查：

```powershell
pwsh -File .\deploy-one-click.ps1 -PrepareOnly
```

正式部署：

```powershell
pwsh -File .\deploy-one-click.ps1
```

## 常用参数

只生成配置，不正式部署：

```powershell
pwsh -File .\deploy-one-click.ps1 -PrepareOnly
```

强制重新下载上游源码：

```powershell
pwsh -File .\deploy-one-click.ps1 -RefreshSource
```

使用你自己已经下载好的源码：

```powershell
pwsh -File .\deploy-one-click.ps1 -SourceRoot D:\cloudflare_temp_email
```

跳过依赖安装：

```powershell
pwsh -File .\deploy-one-click.ps1 -SkipInstall
```

## 默认行为

默认配置更接近偏严格的公开部署场景：

- 禁止匿名用户直接创建邮箱
- 默认用户角色为 `vip`
- 管理员角色为 `admin`

如果你希望访客打开首页就能直接生成邮箱，可在 `one-click.config.jsonc` 中加入：

```jsonc
"disableAnonymousUserCreateEmail": false
```

## 仍需手动完成

脚本不会替你操作 Cloudflare 面板，以下步骤仍需手动完成：

1. 在 `Email Routing` 中为 `mailDomain` 开启收件
2. 添加并验证目标邮箱
3. 配置 `Catch-all -> Send to a Worker -> 后端 Worker`
4. 如果域名之前被旧 DNS、Pages 或 Worker 占用，先清理旧绑定

## 致谢

- 原作者主页：[@dreamhunter2333](https://github.com/dreamhunter2333)
- 上游项目：[dreamhunter2333/cloudflare_temp_email](https://github.com/dreamhunter2333/cloudflare_temp_email)
- 部署思路参考：LinuxDO 社区相关教程
- [【教程】2026版 小白也能看懂的自建Cloudflare临时邮箱教程（域名邮箱）](https://linux.do/t/topic/1666961)
- [用 Claude Code 部署 Cloudflare 临时邮箱，20 分钟搞定](https://linux.do/t/topic/1692459)
- [使用自动化脚本快速搭建 Cloudflare 临时邮箱，3-5 分钟搞定 （更新为自动化）](https://linux.do/t/topic/1783188)
