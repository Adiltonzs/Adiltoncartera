---
title: 为仓库设置代码扫描
shortTitle: 设置代码扫描
intro: '您可以通过添加工作流程到仓库来设置 {% data variables.product.prodname_code_scanning %}。'
product: '{% data reusables.gated-features.code-scanning %}'
permissions: 'If you have write permissions to a repository, you can set up or configure {% data variables.product.prodname_code_scanning %} for that repository.'
redirect_from:
  - /github/managing-security-vulnerabilities/configuring-automated-code-scanning
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/enabling-code-scanning
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/enabling-code-scanning-for-a-repository
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/setting-up-code-scanning-for-a-repository
  - /code-security/secure-coding/setting-up-code-scanning-for-a-repository
  - /code-security/secure-coding/automatically-scanning-your-code-for-vulnerabilities-and-errors/setting-up-code-scanning-for-a-repository
versions:
  fpt: '*'
  ghes: '>=3.0'
  ghae: '*'
type: how_to
topics:
  - Advanced Security
  - Code scanning
  - Actions
  - Repositories
---

<!--For this article in earlier GHES versions, see /content/github/finding-security-vulnerabilities-and-errors-in-your-code-->

{% data reusables.code-scanning.beta %}
{% data reusables.code-scanning.enterprise-enable-code-scanning-actions %}

## 设置 {% data variables.product.prodname_code_scanning %} 的选项

在仓库级别决定如何生成 {% data variables.product.prodname_code_scanning %} 警报，以及使用哪些工具。 {% data variables.product.product_name %} 对 {% data variables.product.prodname_codeql %} 分析提供完全集成的支持，还支持使用第三方工具进行分析。 更多信息请参阅“[关于 {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/about-code-scanning#about-tools-for-code-scanning)”。

{% data reusables.code-scanning.enabling-options %}

## 使用操作设置 {% data variables.product.prodname_code_scanning %}

{% ifversion fpt %}使用操作运行 {% data variables.product.prodname_code_scanning %} 将消耗分钟数。 更多信息请参阅“[关于 {% data variables.product.prodname_actions %} 的计费](/billing/managing-billing-for-github-actions/about-billing-for-github-actions).”{% endif %}

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-security %}
3. 在“{% data variables.product.prodname_code_scanning_capc %} 警报”右侧，单击**设置 {% data variables.product.prodname_code_scanning %}**。 {% ifversion fpt or ghes > 3.0 %}如果 {% data variables.product.prodname_code_scanning %} 缺少，您必须要求组织所有者或仓库管理员启用 {% data variables.product.prodname_GH_advanced_security %}。 更多信息请参阅“[管理组织的安全性和分析设置](/organizations/keeping-your-organization-secure/managing-security-and-analysis-settings-for-your-organization)”或“[管理仓库的安全和分析设置](/github/administering-a-repository/managing-security-and-analysis-settings-for-your-repository)”。{% endif %} !["Set up code scanning" button to the right of "Code scanning" in the Security Overview](/assets/images/help/security/overview-set-up-code-scanning.png)
4. Under "Get started with code scanning", click **Set up this workflow** on the {% data variables.product.prodname_codeql_workflow %} or on a third-party workflow. !["Set up this workflow" button under "Get started with {% data variables.product.prodname_code_scanning %}" heading](/assets/images/help/repository/code-scanning-set-up-this-workflow.png){% ifversion fpt or ghes > 2.22 %}工作流程仅在与仓库中检测到的编程语言相关时才会显示。 {% data variables.product.prodname_codeql_workflow %} 始终显示，但仅在 {% data variables.product.prodname_codeql %} 分析支持仓库中存在的语言时才启用“Set up this workflow（设置此工作流程）”按钮。{% endif %}
5. Optionally, to customize how {% data variables.product.prodname_code_scanning %} scans your code, edit the workflow. For more information, see "[Configuring {% data variables.product.prodname_code_scanning %}](/github/finding-security-vulnerabilities-and-errors-in-your-code/configuring-code-scanning)."

   一般来说，您可以提交 {% data variables.product.prodname_codeql_workflow %} 而不对其做任何更改。 但是，许多第三方工作流程需要额外的配置，因此在提交之前请阅读工作流程中的注释。

   更多信息请参阅“[配置 {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/configuring-code-scanning)。”
6. 使用 **Start commit（开始提交）**下拉菜单，并键入提交消息。 ![开始提交](/assets/images/help/repository/start-commit-commit-new-file.png)
7. 选择您是想直接提交到默认分支，还是创建新分支并启动拉取请求。 ![选择提交位置](/assets/images/help/repository/start-commit-choose-where-to-commit.png)
8. 单击 **Commit new file（提交新文件）**或 **Propose new file（提议新文件）**。

在默认 {% data variables.product.prodname_codeql_workflow %} 中，{% data variables.product.prodname_code_scanning %} 配置为在每次将更改推送到默认分支或任何受保护分支或者对默认分支提出拉取请求时分析代码。 因此，{% data variables.product.prodname_code_scanning %} 将立即开始。

## 批量设置 {% data variables.product.prodname_code_scanning %}

您可以使用脚本在多个仓库中一次设置 {% data variables.product.prodname_code_scanning %}。 If you'd like to use a script to raise pull requests that add a {% data variables.product.prodname_actions %} workflow to multiple repositories, see the [`jhutchings1/Create-ActionsPRs`](https://github.com/jhutchings1/Create-ActionsPRs) repository for an example using Powershell, or [`nickliffen/ghas-enablement`](https://github.com/NickLiffen/ghas-enablement) for teams who do not have Powershell and instead would like to use NodeJS.

## 了解拉取请求检查

设置在拉取请求上运行的每个 {% data variables.product.prodname_code_scanning %} 工作流程始终有至少两个条目列在拉取请求的检查部分中。 工作流程中每个分析作业有一个条目，最后还有一个对应于分析结果的条目。

{% data variables.product.prodname_code_scanning %} 分析检查的名称采用形式："TOOL NAME / JOB NAME (TRIGGER)"。 例如，对于 {% data variables.product.prodname_codeql %}，C++ 代码的分析有条目 "{% data variables.product.prodname_codeql %} / Analyze (cpp) (pull_request)"。 您可以单击 {% data variables.product.prodname_code_scanning %} 分析条目上的**Details（详细信息）**来查看日志记录数据。 这允许您在分析作业失败时调试问题。 例如，对于编译的语言的 {% data variables.product.prodname_code_scanning %} 分析，如果操作无法生成代码，则可能会发生这种情况。

  ![After a scan completes, you can view alerts from a completed scan. For more information, see "<a href="/github/finding-security-vulnerabilities-and-errors-in-your-code/managing-alerts-from-code-scanning">Managing alerts from {% data variables.product.prodname_code_scanning %}</a>."](/assets/images/help/repository/code-scanning-pr-checks.png)

当 {% data variables.product.prodname_code_scanning %} 作业完成后， {% data variables.product.prodname_dotcom %} 检查拉取请求是否添加了任何警报，并将“{% data variables.product.prodname_code_scanning_capc %} 结果/工具名称”条目添加到检查列表中。 在执行至少一次 {% data variables.product.prodname_code_scanning %} 后，您可以单击 **Details（详细信息）**查看分析结果。 如果使用拉取请求将 {% data variables.product.prodname_code_scanning %} 添加到存储库，则当您单击“{% data variables.product.prodname_code_scanning_capc %} 结果/工具名称”检查中的 **Details（详细信息）**时，您最初会看到“Missing analysis（缺少分析）”的消息。

  ![缺少提交消息的分析](/assets/images/help/repository/code-scanning-missing-analysis.png)

### “缺少分析”消息的原因

在 {% data variables.product.prodname_code_scanning %} 分析拉取请求中的代码后，它需要将主题分支（用于创建拉取请求的分支）的分析与基本分支（要合并拉取请求的分支）的分析进行比较。 这允许 {% data variables.product.prodname_code_scanning %} 计算哪些警报是拉取请求新近引入的，哪些是基础分支中已经存在的，以及是否有任何现有的警报通过拉取请求中的更改来修复。 最初，如果使用拉取请求将 {% data variables.product.prodname_code_scanning %} 添加到仓库，则尚未分析基础分支，因此无法计算这些详细信息。 在这种情况下，当您从拉取请求的结果检查中点进时，将看到“Missing analysis for base commit SHA-HASH（缺少基础提交 SHA-HASH 的分析）”消息。

在其他情况下，可能没有分析对拉取请求的基础分支的最新提交。 这些包括：

* 已针对默认分支以外的分支提出拉取请求，并且尚未分析此分支。

  要检查是否扫描了分支，请转到 {% data variables.product.prodname_code_scanning_capc %} 页面，单击 **Branch<（分支）**下拉菜单并选择相关分支。

{% ifversion fpt or ghes > 3.1 %}
  ![从 Branch（分支）下拉菜单中选择一个分支](/assets/images/help/repository/code-scanning-branch-dropdown.png)
{% else %}
  ![从 Branch（分支）下拉菜单中选择一个分支](/assets/images/enterprise/3.1/help/repository/code-scanning-branch-dropdown.png)
{% endif %}

  {% if currentVersion == "free-pro-team@latest" %}Using actions to run {% data variables.product.prodname_code_scanning %} will use minutes. For more information, see "[About billing for {% data variables.product.prodname_actions %}](/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions)."{% endif %}

* 目前正在分析拉取请求的基础分支上的最新提交，分析尚未提供。

  等待几分钟，然后将更改推送到拉取请求以重新触发 {% data variables.product.prodname_code_scanning %}。

* 在分析基础分支上的最新提交时发生了错误，且该提交的分析不可用。

  将一个微小的更改合并到基础分支以触发此最新提交的 {% data variables.product.prodname_code_scanning %}，然后将更改推送到拉取请求以重新触发 {% data variables.product.prodname_code_scanning %}。

## 后续步骤

设置 {% data variables.product.prodname_code_scanning %} 并允许其操作完成后，您可以：

- 查看为此仓库生成的所有 {% data variables.product.prodname_code_scanning %} 警报。 更多信息请参阅“[管理仓库的 {% data variables.product.prodname_code_scanning %} 警报](/code-security/secure-coding/managing-code-scanning-alerts-for-your-repository)”。
- 查看在设置 {% data variables.product.prodname_code_scanning %} 后提交的拉取请求生成的任何警报。 更多信息请参阅“[对拉取请求中的 {% data variables.product.prodname_code_scanning %} 警报分类](/code-security/secure-coding/triaging-code-scanning-alerts-in-pull-requests)”。
- 为已完成的运行设置通知。 更多信息请参阅“[配置通知](/github/managing-subscriptions-and-notifications-on-github/configuring-notifications#github-actions-notification-options)”。
- 查看由 {% data variables.product.prodname_code_scanning %} 分析生成的日志。 更多信息请参阅“[查看 {% data variables.product.prodname_code_scanning %} 日志](/code-security/secure-coding/automatically-scanning-your-code-for-vulnerabilities-and-errors/viewing-code-scanning-logs)”。
- 调查初始设置 {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %} 时发生的任何问题。 更多信息请参阅“[{% data variables.product.prodname_codeql %} 工作流程疑难解答](/code-security/secure-coding/troubleshooting-the-codeql-workflow)”。
- 自定义 {% data variables.product.prodname_code_scanning %} 如何扫描您的仓库中的代码。 更多信息请参阅“[配置 {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/configuring-code-scanning)。”
