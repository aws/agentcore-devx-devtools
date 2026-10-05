# AgentCore DevX DevTools

Shared developer tools for the AgentCore repositories: [agentcore-cli](https://github.com/aws/agentcore-cli), [bedrock-agentcore-sdk-python](https://github.com/aws/bedrock-agentcore-sdk-python), [bedrock-agentcore-sdk-typescript](https://github.com/aws/bedrock-agentcore-sdk-typescript). This repo is the home for all reusable GitHub Actions workflows and composite actions.

## Reusable workflows

The `.github/workflows/` directory contains reusable workflows. Each is invoked via [`workflow_call`](https://docs.github.com/en/actions/using-workflows/reusing-workflows) from a caller workflow in a consuming repo.

`reusable-pr-ai-review.yml` centralizes AgentCore Harness review mechanics while
callers retain their event triggers and repository-specific prompts.

`workflow-metrics.yml` emits `WorkflowRunDuration` for a completed
`workflow_run`, with `WorkflowName`, `Branch`, `Result`, and `OS` dimensions.
Callers provide the namespace, OS, and writer-role secret name, grant
`id-token: write`, and pass `WORKFLOW_SECRETS_READER_ROLE_ARN`. Pin the workflow
to a full commit SHA. The writer role needs `cloudwatch:PutMetricData`.

```yaml
on:
  workflow_run:
    workflows: [canary]
    types: [completed]

permissions:
  id-token: write

jobs:
  metrics:
    uses: aws/agentcore-devx-devtools/.github/workflows/workflow-metrics.yml@<ref>
    with:
      namespace: AgentCoreCLI/Workflows
      os: Linux
      role-secret-name: E2E_AWS_ROLE_ARN
    secrets:
      WORKFLOW_SECRETS_READER_ROLE_ARN: ${{ secrets.WORKFLOW_SECRETS_READER_ROLE_ARN }}
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This project is licensed under the Apache-2.0 License.
