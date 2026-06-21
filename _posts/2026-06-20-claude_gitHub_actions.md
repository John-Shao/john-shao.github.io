
        **所需设置**：

        1. **启用 Amazon Bedrock**：
           * 请求在 Amazon Bedrock 中访问 Claude 模型
           * 对于跨区域模型，请在所有必需的区域中请求访问

        2. **设置 GitHub OIDC 身份提供商**：
           * 提供商 URL：`https://token.actions.githubusercontent.com`
           * 受众：`sts.amazonaws.com`

        3. **为 GitHub Actions 创建 IAM 角色**：
           * 受信任的实体类型：Web 身份
           * 身份提供商：`token.actions.githubusercontent.com`
           * 权限：`AmazonBedrockFullAccess` 策略
           * 为您的特定仓库配置信任策略

        **所需值**：

        设置后，您需要：

        * **AWS\_ROLE\_TO\_ASSUME**：您创建的 IAM 角色的 ARN

        <Tip>
          OIDC 比使用静态 AWS 访问密钥更安全，因为凭证是临时的并自动轮换。
        </Tip>

        有关详细的 OIDC 设置说明，请参阅 [AWS 文档](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)。
      </Accordion>

      <Accordion title="Google Vertex AI">
        **配置 Google Cloud 以允许 GitHub Actions 安全地进行身份验证，而无需存储凭证。**

        > **安全说明**：使用特定于仓库的配置并仅授予最少所需的权限。

        **所需设置**：

        1. **在您的 Google Cloud 项目中启用 API**：
           * IAM Credentials API
           * Security Token Service (STS) API
           * Vertex AI API

        2. **创建工作负载身份联合资源**：
           * 创建工作负载身份池
           * 添加 GitHub OIDC 提供商，具有：
             * 发行者：`https://token.actions.githubusercontent.com`
             * 仓库和所有者的属性映射
             * **安全建议**：使用特定于仓库的属性条件

        3. **创建服务账户**：
           * 仅授予 `Vertex AI User` 角色
           * **安全建议**：为每个仓库创建专用服务账户

        4. **配置 IAM 绑定**：
           * 允许工作负载身份池模拟服务账户
           * **安全建议**：使用特定于仓库的主体集

        **所需值**：

        设置后，您需要：

        * **GCP\_WORKLOAD\_IDENTITY\_PROVIDER**：完整的提供商资源名称
        * **GCP\_SERVICE\_ACCOUNT**：服务账户电子邮件地址

        <Tip>
          工作负载身份联合消除了对可下载服务账户密钥的需求，提高了安全性。
        </Tip>

        有关详细的设置说明，请参阅 [Google Cloud 工作负载身份联合文档](https://cloud.google.com/iam/docs/workload-identity-federation)。
      </Accordion>
    </AccordionGroup>
  </Step>

  <Step title="添加所需的密钥">
    将以下密钥添加到您的仓库（Settings → Secrets and variables → Actions）：

    #### 对于 Claude API（直接）：

    1. **对于 API 身份验证**：
       * `ANTHROPIC_API_KEY`：您的 Claude API 密钥，来自 [console.anthropic.com](https://console.anthropic.com)

    2. **对于 GitHub 应用（如果使用您自己的应用）**：
       * `APP_ID`：您的 GitHub 应用的 ID
       * `APP_PRIVATE_KEY`：私钥 (.pem) 内容

    #### 对于 Google Cloud Vertex AI

    1. **对于 GCP 身份验证**：
       * `GCP_WORKLOAD_IDENTITY_PROVIDER`
       * `GCP_SERVICE_ACCOUNT`

    2. **对于 GitHub 应用（如果使用您自己的应用）**：
       * `APP_ID`：您的 GitHub 应用的 ID
       * `APP_PRIVATE_KEY`：私钥 (.pem) 内容

    #### 对于 Amazon Bedrock

    1. **对于 AWS 身份验证**：
       * `AWS_ROLE_TO_ASSUME`

    2. **对于 GitHub 应用（如果使用您自己的应用）**：
       * `APP_ID`：您的 GitHub 应用的 ID
       * `APP_PRIVATE_KEY`：私钥 (.pem) 内容
  </Step>

  <Step title="创建工作流文件">
    创建与您的云提供商集成的 GitHub Actions 工作流文件。下面的示例显示了 Amazon Bedrock 和 Google Vertex AI 的完整配置：

    <AccordionGroup>
      <Accordion title="Amazon Bedrock 工作流">
        **前置条件：**

        * 启用了 Amazon Bedrock 访问权限，具有 Claude 模型权限
        * GitHub 在 AWS 中配置为 OIDC 身份提供商
        * 具有 Bedrock 权限的 IAM 角色，信任 GitHub Actions

        **所需的 GitHub 密钥：**

        | 密钥名称             | 描述                                |
        | -------------------- | ----------------------------------- |
        | `AWS_ROLE_TO_ASSUME` | Bedrock 访问的 IAM 角色的 ARN       |
        | `APP_ID`             | 您的 GitHub 应用 ID（来自应用设置） |
        | `APP_PRIVATE_KEY`    | 您为 GitHub 应用生成的私钥          |

        ```yaml theme={null}
        name: Claude PR Action

        permissions:
          contents: write
          pull-requests: write
          issues: write
          id-token: write

        on:
          issue_comment:
            types: [created]
          pull_request_review_comment:
            types: [created]
          issues:
            types: [opened, assigned]

        jobs:
          claude-pr:
            if: |
              (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
              (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
              (github.event_name == 'issues' && contains(github.event.issue.body, '@claude'))
            runs-on: ubuntu-latest
            env:
              AWS_REGION: us-west-2
            steps:
              - name: Checkout repository
                uses: actions/checkout@v4

              - name: Generate GitHub App token
                id: app-token
                uses: actions/create-github-app-token@v2
                with:
                  app-id: ${{ secrets.APP_ID }}
                  private-key: ${{ secrets.APP_PRIVATE_KEY }}

              - name: Configure AWS Credentials (OIDC)
                uses: aws-actions/configure-aws-credentials@v4
                with:
                  role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
                  aws-region: us-west-2

              - uses: anthropics/claude-code-action@v1
                with:
                  github_token: ${{ steps.app-token.outputs.token }}
                  use_bedrock: "true"
                  claude_args: '--model us.anthropic.claude-sonnet-4-6 --max-turns 10'
        ```

        <Tip>
          Bedrock 的模型 ID 格式包括区域前缀（例如，`us.anthropic.claude-sonnet-4-6`）。
        </Tip>
      </Accordion>

      <Accordion title="Google Vertex AI 工作流">
        **前置条件：**

        * 在您的 GCP 项目中启用了 Vertex AI API
        * 为 GitHub 配置了工作负载身份联合
        * 具有 Vertex AI 权限的服务账户

        **所需的 GitHub 密钥：**

        | 密钥名称                         | 描述                                      |
        | -------------------------------- | ----------------------------------------- |
        | `GCP_WORKLOAD_IDENTITY_PROVIDER` | 工作负载身份提供商资源名称                |
        | `GCP_SERVICE_ACCOUNT`            | 具有 Vertex AI 访问权限的服务账户电子邮件 |
        | `APP_ID`                         | 您的 GitHub 应用 ID（来自应用设置）       |
        | `APP_PRIVATE_KEY`                | 您为 GitHub 应用生成的私钥                |

        ```yaml theme={null}
        name: Claude PR Action

        permissions:
          contents: write
          pull-requests: write
          issues: write
          id-token: write

        on:
          issue_comment:
            types: [created]
          pull_request_review_comment:
            types: [created]
          issues:
            types: [opened, assigned]

        jobs:
          claude-pr:
            if: |
              (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
              (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
              (github.event_name == 'issues' && contains(github.event.issue.body, '@claude'))
            runs-on: ubuntu-latest
            steps:
              - name: Checkout repository
                uses: actions/checkout@v4

              - name: Generate GitHub App token
                id: app-token
                uses: actions/create-github-app-token@v2
                with:
                  app-id: ${{ secrets.APP_ID }}
                  private-key: ${{ secrets.APP_PRIVATE_KEY }}

              - name: Authenticate to Google Cloud
                id: auth
                uses: google-github-actions/auth@v2
                with:
                  workload_identity_provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
                  service_account: ${{ secrets.GCP_SERVICE_ACCOUNT }}

              - uses: anthropics/claude-code-action@v1
                with:
                  github_token: ${{ steps.app-token.outputs.token }}
                  trigger_phrase: "@claude"
                  use_vertex: "true"
                  claude_args: '--model claude-sonnet-4-5@20250929 --max-turns 10'
                env:
                  ANTHROPIC_VERTEX_PROJECT_ID: ${{ steps.auth.outputs.project_id }}
                  CLOUD_ML_REGION: us-east5
                  VERTEX_REGION_CLAUDE_4_5_SONNET: us-east5
        ```

        <Tip>
          项目 ID 从 Google Cloud 身份验证步骤自动检索，因此您无需对其进行硬编码。
        </Tip>
      </Accordion>
    </AccordionGroup>
  </Step>
</Steps>

<h2 id="troubleshooting">
  故障排除
</h2>

<h3 id="claude-not-responding-to-claude-commands">
  Claude 不响应 @claude 命令
</h3>

验证 GitHub 应用是否正确安装，检查工作流是否已启用，确保 API 密钥在仓库密钥中设置，并确认评论包含 `@claude`（不是 `/claude`）。

<h3 id="ci-not-running-on-claude’s-commits">
  CI 不在 Claude 的提交上运行
</h3>

确保您使用的是 GitHub 应用或自定义应用（不是 Actions 用户），检查工作流触发器是否包含必要的事件，并验证应用权限是否包括 CI 触发器。

<h3 id="authentication-errors">
  身份验证错误
</h3>

确认 API 密钥有效且具有足够的权限。对于 Bedrock/Vertex，检查凭证配置并确保密钥在工作流中正确命名。

<h2 id="advanced-configuration">
  高级配置
</h2>

<h3 id="action-parameters">
  Action 参数
</h3>

Claude Code Action v1 使用简化的配置：

| 参数                  | 描述                                                  | 必需   |
| --------------------- | ----------------------------------------------------- | ------ |
| `prompt`              | Claude 的说明（纯文本或 [skill](/zh-CN/skills) 名称） | 否\*   |
| `claude_args`         | 传递给 Claude Code 的 CLI 参数                        | 否     |
| `plugin_marketplaces` | 插件市场 Git URL 的换行符分隔列表                     | 否     |
| `plugins`             | 执行前要安装的插件名称的换行符分隔列表                | 否     |
| `anthropic_api_key`   | Claude API 密钥                                       | 是\*\* |
| `github_token`        | 用于 API 访问的 GitHub 令牌                           | 否     |
| `trigger_phrase`      | 自定义触发短语（默认："@claude"）                     | 否     |
| `use_bedrock`         | 使用 Amazon Bedrock 而不是 Claude API                 | 否     |
| `use_vertex`          | 使用 Google Vertex AI 而不是 Claude API               | 否     |

\*提示是可选的 - 当对 issue/PR 评论省略时，Claude 响应触发短语\
\*\*对于直接 Claude API 是必需的，对于 Bedrock/Vertex 不是必需的

<h4 id="pass-cli-arguments">
  传递 CLI 参数
</h4>

`claude_args` 参数接受任何 Claude Code CLI 参数：

```yaml theme={null}
claude_args: "--max-turns 5 --model claude-sonnet-4-6 --mcp-config /path/to/config.json"
```

常见参数：

* `--max-turns`：最大对话轮数（默认：10）
* `--model`：要使用的模型（例如，`claude-sonnet-4-6`）
* `--mcp-config`：MCP 配置的路径
* `--allowedTools`：允许的工具的逗号分隔列表。`--allowed-tools` 别名也可以使用。
* `--debug`：启用调试输出

<h3 id="alternative-integration-methods">
  替代集成方法
</h3>

虽然 `/install-github-app` 命令是推荐的方法，但您也可以：

* **自定义 GitHub 应用**：对于需要品牌用户名或自定义身份验证流的组织。创建您自己的 GitHub 应用，具有所需的权限（contents、issues、pull requests），并使用 actions/create-github-app-token action 在您的工作流中生成令牌。
* **手动 GitHub Actions**：直接工作流配置以获得最大灵活性
* **MCP 配置**：Model Context Protocol 服务器的动态加载

有关身份验证、安全和高级配置的详细指南，请参阅 [Claude Code Action 文档](https://github.com/anthropics/claude-code-action/blob/main/docs)。

<h3 id="customizing-claude’s-behavior">
  自定义 Claude 的行为
</h3>

您可以通过两种方式配置 Claude 的行为：

1. **CLAUDE.md**：在您的仓库根目录的 `CLAUDE.md` 文件中定义编码标准、审查标准和项目特定规则。Claude 在创建 PR 和响应请求时将遵循这些指南。查看我们的 [Memory 文档](/zh-CN/memory)了解更多详情。
2. **自定义提示**：在工作流文件中使用 `prompt` 参数提供工作流特定的说明。这允许您为不同的工作流或任务自定义 Claude 的行为。

Claude 在创建 PR 和响应请求时将遵循这些指南。
