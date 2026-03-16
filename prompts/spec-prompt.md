Set-Content -Path prompts\spec-prompt.md -Value @"
You are a senior Angular developer for Stackyon.
Tech Stack: Angular 14, Syncfusion components, TypeScript, NgModules

Task: {ISSUE_TITLE}
Description: {ISSUE_BODY}

Generate specification with:
1. Which existing component to modify
2. Syncfusion pagination component to use (ejs-pager)
3. TypeScript changes needed
4. HTML template changes
5. Any service changes needed
6. Unit test cases

Follow Stackyon standards:
- Angular 14 NgModules only, no standalone
- Syncfusion EJ2 components only
- No inline styles, use SCSS
- takeUntil for Observable cleanup
- No console.log in production code
"@


Set-Content -Path rules\coding-standards.md -Value @"
# Stackyon Coding Standards
## Angular 14
- NgModules only, never standalone components
- Syncfusion EJ2 for all UI components
- HttpClient for all API calls
- environment.ts for all URLs
- takeUntil pattern for Observable cleanup
- SCSS only, no inline styles
- No console.log in production
"@

Set-Content -Path config\github-config.json -Value @"
{
  "angular_repo": "StackyonCorp/stackyon-dev-studio",
  "dotnet_repo": "StackyonCorp/master-api",
  "agents_repo": "Karthik-macharla/stackyon-ai-agents",
  "trigger_label": "ai-task",
  "branch_prefix": "feature/ai",
  "base_branch": "develop"
}
"@

Set-Content -Path .github\workflows\task-listener.yml -Value @"
name: AI Task Listener - Stackyon

on:
  issues:
    types: [opened, labeled]

jobs:
  detect-ai-task:
    if: contains(github.event.issue.labels.*.name, 'ai-task')
    runs-on: ubuntu-latest
    steps:
      - name: Post Comment
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '## AI Agent Pipeline Triggered!\n\n- Task detected: ' + context.payload.issue.title + '\n- Generating specification...\n- Copilot coding agent will be assigned shortly.'
            })
"@

