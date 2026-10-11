# GitHub Actions 部署与上游同步

## 首次配置

在仓库 Settings → Secrets and variables → Actions 中配置必需项。密钥应使用 Repository secrets；非敏感设置也可使用 Variables。

| 名称 | 用途 |
| --- | --- |
| `CLOUDFLARE_API_TOKEN` | 部署 Worker、管理所需资源 |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare 账户 |
| `DOMAIN` | 邮件域名 JSON 数组，例如 `["example.com"]` |
| `ADMIN` | 管理员邮箱 |
| `JWT_SECRET` | JWT 签名密钥 |

`D1_DATABASE_ID` 和 `KV_NAMESPACE_ID` 可选；未提供时工作流会按项目名称查找或创建资源。`CUSTOM_DOMAIN`、`R2_BUCKET_NAME`、`PROJECT_LINK`、`AI_MODEL`、`CF_EMAIL`、`ANALYSIS_CACHE` 以及 LinuxDo OAuth 设置按需配置。不要把密钥提交到仓库文件。

## 自动同步与部署

`sync-upstream.yml` 每周一北京时间 10:17 检查 `maillab/cloud-mail` 的 `main`。有新提交且能够干净合并时，工作流会合并到本仓库 `main`，然后触发 `deploy-cloudflare.yml`。

如果上游更新和本仓库定制配置发生冲突，同步会停止，不会强制覆盖 `main`。请在 GitHub Actions 中查看失败日志，解决冲突后重新运行 `Sync upstream` 工作流。

若部署所需的 Cloudflare 配置未齐全，代码仍会同步，但部署步骤会跳过并在 Actions 摘要中列出缺少的设置名称。配置完成后，可从 Actions 手动运行 `Deploy cloud-mail to Cloudflare Workers`。

## 手动部署

在 GitHub 仓库的 Actions 页面手动运行 `Deploy cloud-mail to Cloudflare Workers`。普通代码更新也会触发该工作流。
