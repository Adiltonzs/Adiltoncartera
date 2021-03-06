---
title: ワークフローをトリガーするイベント
intro: '{% data variables.product.product_name %} で特定のアクティビティが実行された時、スケジュールした時間、または {% data variables.product.product_name %} 外でイベントが発生した時にワークフローを実行できるよう設定できます。'
product: '{% data reusables.gated-features.actions %}'
miniTocMaxHeadingLevel: 3
redirect_from:
  - /articles/events-that-trigger-workflows
  - /github/automating-your-workflow-with-github-actions/events-that-trigger-workflows
  - /actions/automating-your-workflow-with-github-actions/events-that-trigger-workflows
  - /actions/reference/events-that-trigger-workflows
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
shortTitle: ワークフローをトリガーするイベント
---

{% data reusables.actions.enterprise-beta %}
{% data reusables.actions.enterprise-github-hosted-runners %}
{% data reusables.actions.ae-beta %}

## ワークフローイベントを設定する

`on` ワークフロー構文を使用して、1 つ以上のイベントに対して実行するようにワークフローを設定できます。 詳しい情報については、「[{% data variables.product.prodname_actions %} のワークフロー構文](/articles/workflow-syntax-for-github-actions#on)」を参照してください。

{% data reusables.github-actions.actions-on-examples %}

{% note %}

**ノート:** `GITHUB_TOKEN`を使って新しいワークフローの実行をトリガーすることはできません。 詳しい情報については「[個人アクセストークンを使った新しいワークフローのトリガー](#triggering-new-workflows-using-a-personal-access-token)」を参照してください。

{% endnote %}

ワークフローの実行がトリガーされるには、以下のステップが生じます。

1. リポジトリでイベントが発生し、結果のイベントにはコミット SHA と Git ref が関連付けられます。
2. リポジトリ内の関連づけられたコミットSHAもしくはGit refにおける `.github/workflows`ディレクトリ内でワークフローファイルが検索される。 ワークフローファイルは、コミットSHAあるいはGit refを考慮した上で存在していなければなりません。

  たとえば、イベントが特定のリポジトリブランチで発生したなら、ワークフローファイルはそのブランチ上でリポジトリ内に存在しなければなりません。
1. そのコミットSHA及びGit refのワークフローファイルが調べられ、トリガーを起こしたイベントにマッチする`on:`の値を持つワークフローについて新しい実行がトリガーされる。

  ワークフローの実行は、イベントをトリガーしたのと同じコミットSHA及びGit refにあるリポジトリのコード上で実行されます。 ワークフローを実行すると、{% data variables.product.product_name %} はランナー環境において `GITHUB_SHA` (コミット SHA) および `GITHUB_REF` (Git ref) 環境変数を設定します。 詳しい情報については、「[環境変数の利用](/actions/automating-your-workflow-with-github-actions/using-environment-variables)」を参照してください。

## スケジュールしたイベント

`schedule` イベントを使用すると、スケジュールされた時間にワークフローをトリガーできます。

{% data reusables.actions.schedule-delay %}

### `スケジュール`

| webhook イベントのペイロード | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF`                                                                                                                                                                                                                                                                          |
| ------------------ | ---------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| n/a                | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ | スケジュールしたワークフローを実行するよう設定したとき。 スケジュールしたワークフローは、[POSIX クーロン構文](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07)を使用します。 詳しい情報については、「[イベントでワークフローをトリガーする](/articles/configuring-a-workflow/#triggering-a-workflow-with-events)」を参照してください。 |

{% data reusables.repositories.actions-scheduled-workflow-example %}

クーロン構文では、スペースで分けられた 5 つのフィールドがあり、各フィールドは時間の単位を表わします。

```
┌───────────── 分 (0 - 59)
│ ┌───────────── 時間 (0 - 23)
│ │ ┌───────────── 日 (1 - 31)
│ │ │ ┌───────────── 月 (1 - 12 または JAN-DEC)
│ │ │ │ ┌───────────── 曜日 (0 - 6 または SUN-SAT)
│ │ │ │ │                                   
│ │ │ │ │
│ │ │ │ │
* * * * *
```

5 つのフィールドいずれにおいても、以下の演算子を使用できます:

| 演算子 | 説明         | サンプル                                                            |
| --- | ---------- | --------------------------------------------------------------- |
| *   | 任意の値       | `* * * * *` 毎日、毎分実行します。                                         |
| ,   | 値リストの区切り文字 | `2,10 4,5 * * *` 毎日、午前 4 時および午前 5 時の、2 分および 10 分に実行します。         |
| -   | 値の範囲       | `0 4-6 * * *` 午前 4 時、5 時、および 6 時の、0 分に実行します。                    |
| /   | ステップ値      | `20/15 * * * *` 20 分から 59 分までの間で、15 分おきに実行します (20 分、35 分、50 分)。 |

{% note %}

**注釈:** {% data variables.product.prodname_actions %} は、非標準的構文　(`@yearly`、`@monthly`、`@weekly`、`@daily`、`@hourly`、`@reboot`) をサポートしていません。

{% endnote %}

[crontab guru](https://crontab.guru/) を使うと、クーロン構文の生成および実行時間の確認に役立ちます。 また、クーロン構文の生成を支援するため、[crontab guru のサンプル](https://crontab.guru/examples.html)リストもあります。

ワークフロー内のクーロン構文を最後に修正したユーザには、スケジュールされたワークフローの通知が送られます。 詳しい情報については「[ワークフローの実行の通知](/actions/guides/about-continuous-integration#notifications-for-workflow-runs)」を参照してください。

## 手動イベント

ワークフローの実行を手動でトリガーできます。 リポジトリ内の特定のワークフローをトリガーするには、`workflow_dispatch` イベントを使用します。 リポジトリで複数のワークフローをトリガーし、カスタムイベントとイベントタイプを作成するには、`repository_dispatch` イベントを使用します。

### `workflow_dispatch`

| webhook イベントのペイロード                                               | アクティビティタイプ | `GITHUB_SHA`               | `GITHUB_REF`    |
| ---------------------------------------------------------------- | ---------- | -------------------------- | --------------- |
| [workflow_dispatch](/webhooks/event-payloads/#workflow_dispatch) | n/a        | `GITHUB_REF` ブランチ上の直近のコミット | ディスパッチを受信したブランチ |

カスタム定義の入力プロパティ、デフォルトの入力値、イベントに必要な入力をワークフローで直接設定できます。 ワークフローが実行されると、 `github.event.inputs` コンテキスト内の入力値にアクセスできます。 詳細については、「[コンテキスト](/actions/learn-github-actions/contexts)」を参照してください。

You can manually trigger a workflow run using the {% data variables.product.prodname_dotcom %} API and from {% data variables.product.prodname_dotcom %}. 詳しい情報については、「[ワークフローを手動で実行する](/actions/managing-workflow-runs/manually-running-a-workflow)」を参照してください。

 {% data variables.product.prodname_dotcom %} でイベントをトリガーすると、{% data variables.product.prodname_dotcom %} で `ref` と `inputs` を直接入力できます。 詳しい情報については、「[アクションで入力と出力を使用する](/actions/learn-github-actions/finding-and-customizing-actions#using-inputs-and-outputs-with-an-action)」を参照してください。

 REST API を使用してカスタム `workflow_dispatch` webhook イベントをトリガーするには、`POST` リクエストを {% data variables.product.prodname_dotcom %} API エンドポイントに送信し、`ref` および必要な `inputs` を入力する必要があります。 詳細については、「[ワークフローディスパッチイベントの作成](/rest/reference/actions/#create-a-workflow-dispatch-event)」REST API エンドポイントを参照してください。

#### サンプル

`workflow_dispatch`イベントを使うには、GitHub Actionsのワークフローファイル中にトリガーとして含めなければなりません。 以下の例では、手動でトリガーされた場合にのみワークフローが実行されます。

```yaml
on: workflow_dispatch
```

#### ワークフロー設定の例

この例では、 `name`と`home`の入力を定義し、`github.event.inputs.name`および`github.event.inputs.home`コンテキストを使用してそれらを出力します。 `home`が提供されなければ、デフォルト値の'The Octoverse'が出力されます。

{% raw %}
```yaml
name: Manually triggered workflow
on:
  workflow_dispatch:
    inputs:
      name:
        description: 'Person to greet'
        required: true
        default: 'Mona the Octocat'
      home:
        description: 'location'
        required: false
        default: 'The Octoverse'

jobs:
  say_hello:
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "Hello ${{ github.event.inputs.name }}!"
          echo "- in ${{ github.event.inputs.home }}!"
```
{% endraw %}

### `repository_dispatch`

| webhook イベントのペイロード                                                   | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------------------------- | ---------- | ----------------- | ------------ |
| [repository_dispatch](/webhooks/event-payloads/#repository_dispatch) | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.github-actions.branch-requirement %}

{% data variables.product.product_name %} の外部で生じるアクティビティのためにワークフローをトリガーしたい場合、{% data variables.product.prodname_dotcom %} API を使って、[`repository_dispatch`](/webhooks/event-payloads/#repository_dispatch) と呼ばれる webhook イベントをトリガーできます。 詳細については、「[リポジトリディスパッチ イベントの作成](/rest/reference/repos#create-a-repository-dispatch-event)」を参照してください。

カスタム `repository_dispatch` webhook イベントをトリガーするには、{% data variables.product.product_name %} API エンドポイントに `POST` リクエストを送信して、アクティビティのタイプを説明する `event_type` 名を提供する必要があります。 ワークフローの実行をトリガーするには、`repository_dispatch` イベントを使用するようワークフローを設定する必要もあります。

#### サンプル

デフォルトでは、すべての`event_types`がワークフローの実行をトリガーします。 特定の`event_type`の値が`repository_dispatch` webhookのペイロード内で送信された時にのみワークフローが実行されるように制限できます。 リポジトリのディスパッチイベントを生成する際に、`repository_dispatch`ペイロード内で送信されるイベントの種類を定義します。

```yaml
on:
  repository_dispatch:
    types: [opened, deleted]
```

## webhook イベント

webhook イベントが {% data variables.product.product_name %} で生成されたときに実行されるようにワークフローを設定できます イベントによっては、そのイベントをトリガーするアクティビティタイプが 複数あります。 イベントをトリガーするアクティビティタイプが複数ある場合は、ワークフローの実行をトリガーするアクティビティタイプを指定できます。 詳しい情報については、「[webhook](/webhooks)」を参照してください。

すべての webhook イベントがワークフローをトリガーするわけではありません。 使用可能な webhook イベントとそのペイロードの完全なリストについては、「[webhook イベントとペイロード](/developers/webhooks-and-events/webhook-events-and-payloads)」を参照してください。

### `check_run`

`check_run` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[チェック実行](/rest/reference/checks#runs)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                 | アクティビティタイプ                                                    | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------- | ------------------------------------------------------------- | ----------------- | ------------ |
| [`check_run`](/webhooks/event-payloads/#check_run) | - `created`<br/>- `rerequested`<br/>- `completed` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、チェック実行が `rerequested` または `completed` であったときにワークフローを実行できます。

```yaml
on:
  check_run:
    types: [rerequested, completed]
```

### `check_suite`

`check_suite` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[チェックスイート](/rest/reference/checks#suites)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

{% note %}

**ノート:** 再帰的なワークフローを避けるために、このイベントは{% data variables.product.prodname_actions %}によってチェックスイートが生成されている場合にはワークフローをトリガーしません。

{% endnote %}

| webhook イベントのペイロード                                     | アクティビティタイプ                                                                 | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------------------ | -------------------------------------------------------------------------- | ----------------- | ------------ |
| [`check_suite`](/webhooks/event-payloads/#check_suite) | - `completed`<br/>- `requested`<br/>- `rerequested`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、チェック実行が `rerequested` または `completed` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  check_suite:
    types: [rerequested, completed]
```

### `create`

誰かがブランチまたはタグを作成し、それによって `create` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[リファレンスの作成](/rest/reference/git#create-a-reference)」を参照してください。

| webhook イベントのペイロード                           | アクティビティタイプ | `GITHUB_SHA`           | `GITHUB_REF`   |
| -------------------------------------------- | ---------- | ---------------------- | -------------- |
| [`create`](/webhooks/event-payloads/#create) | n/a        | 直近でブランチまたはタグが作成されたコミット | 作成されたブランチまたはタグ |

たとえば、`create` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  create
```

### `delete`

誰かがブランチまたはタグを作成し、それによって `delete` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[リファレンスの削除](/rest/reference/git#delete-a-reference)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                           | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------- | ---------- | ----------------- | ------------ |
| [`delete`](/webhooks/event-payloads/#delete) | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ    |

たとえば、`delete` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  delete
```

### `deployment`

誰かがデプロイメントを作成し、それによって `deployment` イベントがトリガーされるときにワークフローを実行します。 コミット SHA 付きで作成されたデプロイメントには Git ref がない場合があります。 REST API の詳細については、「[デプロイメント](/rest/reference/repos#deployments)」を参照してください。

| webhook イベントのペイロード                                   | アクティビティタイプ | `GITHUB_SHA` | `GITHUB_REF`                 |
| ---------------------------------------------------- | ---------- | ------------ | ---------------------------- |
| [`deployment`](/webhooks/event-payloads/#deployment) | n/a        | デプロイされるコミット  | デプロイされるブランチまたはタグ (コミットの場合は空) |

たとえば、`deployment` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  deployment
```

### `deployment_status`

サードパーティプロバイダーがデプロイメントステータスを提供し、それによって `deployment_status` イベントがトリガーされるときにワークフローを実行します。 コミット SHA 付きで作成されたデプロイメントには Git ref がない場合があります。 REST API の詳細については、「[デプロイメントステータスの作成](/rest/reference/repos#create-a-deployment-status)」を参照してください。

| webhook イベントのペイロード                                                 | アクティビティタイプ | `GITHUB_SHA` | `GITHUB_REF`                 |
| ------------------------------------------------------------------ | ---------- | ------------ | ---------------------------- |
| [`deployment_status`](/webhooks/event-payloads/#deployment_status) | n/a        | デプロイされるコミット  | デプロイされるブランチまたはタグ (コミットの場合は空) |

たとえば、`deployment_status` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  deployment_status
```

{% note %}

**注釈:** デプロイメントステータスの状態が `inactive` に設定されている場合、webhook イベントは作成されません。

{% endnote %}

{% ifversion fpt %}
### `ディスカッション`

Runs your workflow anytime the `discussion` event occurs. {% data reusables.developer-site.multiple_activity_types %} For information about the GraphQL API, see "[Discussions](/graphql/guides/using-the-graphql-api-for-discussions)."

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                 | アクティビティタイプ                                                                                                                                                                                                                                                                                              | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------ |
| [`ディスカッション`](/webhooks/event-payloads/#discussion) | - `created`<br/>- `edited`<br/>- `deleted`<br/>- `transferred`<br/>- `pinned`<br/>- `unpinned`<br/>- `labeled`<br/>- `unlabeled`<br/>- `locked`<br/>- `unlocked`<br/>- `category_changed`<br/> - `answered`<br/> - `unanswered` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

For example, you can run a workflow when a discussion has been `created`, `edited`, or `answered`.

```yaml
on:
  discussion:
    types: [created, edited, answered]
```

### `discussion_comment`

Runs your workflow anytime the `discussion_comment` event occurs. {% data reusables.developer-site.multiple_activity_types %} For information about the GraphQL API, see "[Discussions](/graphql/guides/using-the-graphql-api-for-discussions)."

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                                                                     | アクティビティタイプ                                                        | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------- | ----------------- | ------------ |
| [`discussion_comment`](/developers/webhooks-and-events/webhook-events-and-payloads#discussion_comment) | - `created`<br/>- `edited`<br/>- `deleted`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、Issue コメントが `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  discussion_comment:
    types: [created, deleted]
```
{% endif %}

### `フォーク`

誰かがリポジトリをフォークし、それによって `deployment_status` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[フォークの作成](/rest/reference/repos#create-a-fork)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                       | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| ---------------------------------------- | ---------- | ----------------- | ------------ |
| [`フォーク`](/webhooks/event-payloads/#fork) | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ    |

たとえば、`fork` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  fork
```

### `gollum`

誰かが Wiki ページを作成または更新し、それによって `gollum` イベントがトリガーされるときにワークフローを実行します。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                           | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------- | ---------- | ----------------- | ------------ |
| [`gollum`](/webhooks/event-payloads/#gollum) | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ    |

たとえば、`gollum` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  gollum
```

### `issue_comment`

`issue_comment` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[Issue コメント](/developers/webhooks-and-events/webhook-events-and-payloads#issue_comment)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                                                           | アクティビティタイプ                                                        | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | ----------------- | ------------ |
| [`issue_comment`](/developers/webhooks-and-events/webhook-events-and-payloads#issue_comment) | - `created`<br/>- `edited`<br/>- `deleted`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、Issue コメントが `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  issue_comment:
    types: [created, deleted]
```

`issue_comment`イベントは、IssueやPull Requestにコメントされたときに生じます。 `issue_comment`イベントがIssueで生じたのかPull Requestで生じたのかを判断するには、イベントのペイロードの`issue.pull_request`属性をチェックして、それをジョブをスキップする条件として利用できます。

たとえば、コメントイベントがPull Requestで生じたときに`pr_commented`を実行し、コメントイベントがIssueで生じたときに`issue_commented`ジョブを実行するようにできます。

{% raw %}
```yaml
on: issue_comment

jobs:
  pr_commented:
    # このジョブはプルリクエストコメントに対してのみ実行されます
    name: PR comment
    if: ${{ github.event.issue.pull_request }}
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "Comment on PR #${{ github.event.issue.number }}"

  issue_commented:
    # このジョブは Issue comment に対してのみ実行されます
    name: Issue comment
    if: ${{ !github.event.issue.pull_request }}
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "Comment on issue #${{ github.event.issue.number }}"
```
{% endraw %}

### `issues`

`Issue` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[Issue](/rest/reference/issues)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                           | アクティビティタイプ                                                                                                                                                                                                                                                                                                                                                             | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------ |
| [`issues`](/webhooks/event-payloads/#issues) | - `opened`<br/>- `edited`<br/>- `deleted`<br/>- `transferred`<br/>- `pinned`<br/>- `unpinned`<br/>- `closed`<br/>- `reopened`<br/>- `assigned`<br/>- `unassigned`<br/>- `labeled`<br/>- `unlabeled`<br/>- `locked`<br/>- `unlocked`<br/>- `milestoned`<br/> - `demilestoned` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、Issue が `opened`、`edited`、または `milestoned` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  issues:
    types: [opened, edited, milestoned]
```

### `ラベル`

`label` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[ラベル](/rest/reference/issues#labels)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                       | アクティビティタイプ                                                        | `GITHUB_SHA`      | `GITHUB_REF` |
| ---------------------------------------- | ----------------------------------------------------------------- | ----------------- | ------------ |
| [`ラベル`](/webhooks/event-payloads/#label) | - `created`<br/>- `edited`<br/>- `deleted`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、ラベルが `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  label:
    types: [created, deleted]
```

### `マイルストーン`

`milestone` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[マイルストーン](/rest/reference/issues#milestones)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                               | アクティビティタイプ                                                                                                  | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- | ----------------- | ------------ |
| [`マイルストーン`](/webhooks/event-payloads/#milestone) | - `created`<br/>- `closed`<br/>- `opened`<br/>- `edited`<br/>- `deleted`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえばマイルストーンが`opened`あるいは`deleted`になったときにワークフローを実行できます。

```yaml
on:
  milestone:
    types: [opened, deleted]
```

### `page_build`

誰かが {% data variables.product.product_name %} ページ対応のブランチを作成し、それによって `page_build` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[ページ](/rest/reference/repos#pages)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                   | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| ---------------------------------------------------- | ---------- | ----------------- | ------------ |
| [`page_build`](/webhooks/event-payloads/#page_build) | n/a        | デフォルトブランチの直近のコミット | n/a          |

たとえば、`page_build` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  page_build
```

### `project`

`project` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[プロジェクト](/rest/reference/projects)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                             | アクティビティタイプ                                                                                                                          | `GITHUB_SHA`      | `GITHUB_REF` |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------ |
| [`project`](/webhooks/event-payloads/#project) | - `created`<br/>- `updated`<br/>- `closed`<br/>- `reopened`<br/>- `edited`<br/>- `deleted`<br/> | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、プロジェクトが `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  project:
    types: [created, deleted]
```

### `project_card`

`project_card` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[プロジェクトカード](/rest/reference/projects#cards)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                       | アクティビティタイプ                                                                                                     | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ----------------- | ------------ |
| [`project_card`](/webhooks/event-payloads/#project_card) | - `created`<br/>- `moved`<br/>- `converted` to an issue<br/>- `edited`<br/>- `deleted` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、プロジェクトカードが `opened` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  project_card:
    types: [created, deleted]
```

### `project_column`

`project_column` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[プロジェクト列](/rest/reference/projects#columns)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                           | アクティビティタイプ                                                                  | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------------------------ | --------------------------------------------------------------------------- | ----------------- | ------------ |
| [`project_column`](/webhooks/event-payloads/#project_column) | - `created`<br/>- `updated`<br/>- `moved`<br/>- `deleted` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、プロジェクト列が `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  project_column:
    types: [created, deleted]
```

### `public`

誰かがプライベートリポジトリをパブリックにし、それによって `public` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[リポジトリの編集](/rest/reference/repos#edit)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                           | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------- | ---------- | ----------------- | ------------ |
| [`public`](/webhooks/event-payloads/#public) | n/a        | デフォルトブランチの直近のコミット | デフォルトブランチ    |

たとえば、`public` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  public
```

### `pull_request`

`pull_request` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} For information about the REST API, see "[Pull requests](/rest/reference/pulls)."

{% note %}

**ノート:**
- By default, a workflow only runs when a `pull_request`'s activity type is `opened`, `synchronize`, or `reopened`. 他のアクティビティタイプについてもワークフローをトリガーするには、`types` キーワードを使用してください。
- Workflows will not run on `pull_request` activity if the pull request has a merge conflict. The merge conflict must be resolved first.

{% endnote %}

| webhook イベントのペイロード                                       | アクティビティタイプ                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | `GITHUB_SHA`                  | `GITHUB_REF`                           |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- | -------------------------------------- |
| [`pull_request`](/webhooks/event-payloads/#pull_request) | - `assigned`<br/>- `unassigned`<br/>- `labeled`<br/>- `unlabeled`<br/>- `opened`<br/>- `edited`<br/>- `closed`<br/>- `reopened`<br/>- `synchronize`<br/>- `converted_to_draft`<br/>- `ready_for_review`<br/>- `locked`<br/>- `unlocked` <br/>- `review_requested` <br/>- `review_request_removed`{% ifversion fpt or ghes > 3.0 or ghae %} <br/>- `auto_merge_enabled` <br/>- `auto_merge_disabled`{% endif %} | `GITHUB_REF` ブランチ上の直近のマージコミット | PR マージブランチ `refs/pull/:prNumber/merge` |

デフォルトのアクティビティタイプを拡大または制限するには、`types` キーワードを使用します。 詳しい情報については、「[{% data variables.product.prodname_actions %}のワークフロー構文](/articles/workflow-syntax-for-github-actions#onevent_nametypes)」を参照してください。

たとえば、プルリクエストが `assigned`、`opened`、`synchronize`、または `reopened` だったときにワークフローを実行できます。

```yaml
on:
  pull_request:
    types: [assigned, opened, synchronize, reopened]
```

{% data reusables.developer-site.pull_request_forked_repos_link %}

### `pull_request_review`

`pull_request_review` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} For information about the REST API, see "[Pull request reviews](/rest/reference/pulls#reviews)."

| webhook イベントのペイロード                                                     | アクティビティタイプ                                                 | `GITHUB_SHA`                  | `GITHUB_REF`                           |
| ---------------------------------------------------------------------- | ---------------------------------------------------------- | ----------------------------- | -------------------------------------- |
| [`pull_request_review`](/webhooks/event-payloads/#pull_request_review) | - `submitted`<br/>- `edited`<br/>- `dismissed` | `GITHUB_REF` ブランチ上の直近のマージコミット | PR マージブランチ `refs/pull/:prNumber/merge` |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、プルリクエストレビューが `edited` または `dismissed` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  pull_request_review:
    types: [edited, dismissed]
```

{% data reusables.developer-site.pull_request_forked_repos_link %}

### `pull_request_review_comment`

プルリクエストの統合 diff へのコメントが変更され、それによって `pull_request_review_comment` イベントがトリガーされるときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} For information about the REST API, see [Review comments](/rest/reference/pulls#comments).

| webhook イベントのペイロード                                                                     | アクティビティタイプ                                             | `GITHUB_SHA`                  | `GITHUB_REF`                           |
| -------------------------------------------------------------------------------------- | ------------------------------------------------------ | ----------------------------- | -------------------------------------- |
| [`pull_request_review_comment`](/webhooks/event-payloads/#pull_request_review_comment) | - `created`<br/>- `edited`<br/>- `deleted` | `GITHUB_REF` ブランチ上の直近のマージコミット | PR マージブランチ `refs/pull/:prNumber/merge` |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、プルリクエストレビューコメントが `created` または `deleted` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  pull_request_review_comment:
    types: [created, deleted]
```

{% data reusables.developer-site.pull_request_forked_repos_link %}

{% ifversion fpt or ghes > 2.22 or ghae %}

### `pull_request_target`

このイベントは`pull_request`のようにマージコミット内ではなく、Pull Requestのベースのコンテキストで実行されます。  これにより、リポジトリを変更したり、ワークフローで使うシークレットを盗んだりするような、Pull Requestのヘッドからの安全ではないワークフローのコードが実行されるのを避けられます。 このイベントでは、イベントペイロードの内容に基づいて、プルリクエストにラベルを付けてコメントを付けるワークフローを作成するようなことができます。

{% warning %}

**警告:** `pull_request_target`イベントは、フォークからトリガーされた場合であっても、リポジトリトークンの読み書きが許可されており、シークレットにアクセスできます。 ワークフローはPull Requestのベースのコンテキストで実行されますが、このイベントでPull Requestから信頼できないコードをチェックアウトしたり、ビルドしたり、実行したりしないようにしなければなりません。 加えて、ベースブランチと同じスコープを共有するキャッシュがあり、キャッシュが汚染されることを避けるために、キャッシュの内容が変更されている可能性があるなら、キャッシュを保存するべきではありません。 詳細については、GitHub Security Lab Web サイトの「[GitHub Actions とワークフローを安全に保つ: pwn リクエストの防止](https://securitylab.github.com/research/github-actions-preventing-pwn-requests)」を参照してください。

{% endwarning %}

| webhook イベントのペイロード                                              | アクティビティタイプ                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | `GITHUB_SHA`       | `GITHUB_REF` |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ------------ |
| [`pull_request_target`](/webhooks/event-payloads/#pull_request) | - `assigned`<br/>- `unassigned`<br/>- `labeled`<br/>- `unlabeled`<br/>- `opened`<br/>- `edited`<br/>- `closed`<br/>- `reopened`<br/>- `synchronize`<br/>- `converted_to_draft`<br/>- `ready_for_review`<br/>- `locked`<br/>- `unlocked` <br/>- `review_requested` <br/>- `review_request_removed`{% ifversion fpt or ghes > 3.0 or ghae %} <br/>- `auto_merge_enabled` <br/>- `auto_merge_disabled`{% endif %} | PR ベースブランチの直近のコミット | PR ベースブランチ   |

デフォルトでは、ワークフローは、`pull_request_target` のアクティビティタイプが `opened`、`synchronize`、または `reopened` のときにのみ実行されます。 他のアクティビティタイプについてもワークフローをトリガーするには、`types` キーワードを使用してください。 詳しい情報については、「[{% data variables.product.prodname_actions %}のワークフロー構文](/articles/workflow-syntax-for-github-actions#onevent_nametypes)」を参照してください。

たとえば、プルリクエストが `assigned`、`opened`、`synchronize`、または `reopened` だったときにワークフローを実行できます。

```yaml
on:
  pull_request_target:
    types: [assigned, opened, synchronize, reopened]
```

{% endif %}

### `プッシュ`

{% note %}

**ノート：** GitHub Actionsが利用できるwebhookのペイロードには、`commit`オブジェクト中の`added`、`removed`、`modified`属性は含まれません。 完全なcommitオブジェクトは、REST APIを使って取得できます。 詳しい情報については、「[1つのコミットの取得](/rest/reference/repos#get-a-commit)」を参照してください。

{% endnote %}

誰かがリポジトリブランチにプッシュし、それによって `push` イベントがトリガーされるときにワークフローを実行します。

| webhook イベントのペイロード                       | アクティビティタイプ | `GITHUB_SHA`                                  | `GITHUB_REF` |
| ---------------------------------------- | ---------- | --------------------------------------------- | ------------ |
| [`プッシュ`](/webhooks/event-payloads/#push) | n/a        | プッシュされたコミット、ただし (デフォルトブランチの際に) ブランチを削除する場合を除く | 更新された ref    |

たとえば、`push` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  push
```

### `registry_package`

パッケージが`published`もしくは`updated`されるとワークフローを実行します。 詳しい情報については「[{% data variables.product.prodname_registry %}でのパッケージ管理](/github/managing-packages-with-github-packages)」を参照してください。

| webhook イベントのペイロード                                      | アクティビティタイプ                          | `GITHUB_SHA`    | `GITHUB_REF`          |
| ------------------------------------------------------- | ----------------------------------- | --------------- | --------------------- |
| [`registry_package`](/webhooks/event-payloads/#package) | - `published`<br/>- `updated` | 公開されたパッケージのコミット | 公開されたパッケージのブランチもしくはタグ |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、パッケージが`published`されたときにワークフローを実行できます。

```yaml
on:
  registry_package:
    types: [published]
```

### `リリース`

{% note %}

**注釈:** `release` イベントは、ドラフトリリースではトリガーされません。

{% endnote %}

`release` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[リリース](/rest/reference/repos#releases)」を参照してください。

| webhook イベントのペイロード                          | アクティビティタイプ                                                                                                                                                      | `GITHUB_SHA`       | `GITHUB_REF` |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ------------ |
| [`リリース`](/webhooks/event-payloads/#release) | - `published` <br/>- `unpublished` <br/>- `created` <br/>- `edited` <br/>- `deleted` <br/>- `prereleased`<br/> - `released` | リリースのタグが付いた直近のコミット | リリースのタグ      |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、リリースが `published` だったときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  release:
    types: [published]
```

{% note %}

**注釈:** `prereleased` タイプは、ドラフトリリースから公開されたプレリリースではトリガーされませんが、`published` タイプはトリガーされます。 安定版*および*プレリリースの公開時にワークフローを実行する場合は、`released` および `prereleased` ではなく `published` にサブスクライブします。

{% endnote %}

### `ステータス`

Git コミットのステータスが変更された、それによって `status` イベントがトリガーされるときにワークフローを実行します。 REST API の詳細については、「[ステータス](/rest/reference/repos#statuses)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                          | アクティビティタイプ | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------- | ---------- | ----------------- | ------------ |
| [`ステータス`](/webhooks/event-payloads/#status) | n/a        | デフォルトブランチの直近のコミット | n/a          |

たとえば、`status` イベントが発生したときにワークフローを実行する例は、次のとおりです。

```yaml
on:
  status
```

### `Watch`

`watch` イベントが発生したときにワークフローを実行します。 {% data reusables.developer-site.multiple_activity_types %} REST API の詳細については、「[Star を付ける](/rest/reference/activity#starring)」を参照してください。

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                         | アクティビティタイプ  | `GITHUB_SHA`      | `GITHUB_REF` |
| ------------------------------------------ | ----------- | ----------------- | ------------ |
| [`Watch`](/webhooks/event-payloads/#watch) | - `started` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

たとえば、誰かがリポジトリに Star を付け、それが Watch イベントをトリガーする `started` アクティブタイプである場合にワークフローを実行する例は、次のとおりです。

```yaml
on:
  watch:
    types: [started]
```

{% ifversion fpt or ghes > 2.22 or ghae %}

### `workflow_run`

{% data reusables.webhooks.workflow_run_desc %}

{% data reusables.github-actions.branch-requirement %}

| webhook イベントのペイロード                                       | アクティビティタイプ                            | `GITHUB_SHA`      | `GITHUB_REF` |
| -------------------------------------------------------- | ------------------------------------- | ----------------- | ------------ |
| [`workflow_run`](/webhooks/event-payloads/#workflow_run) | - `completed`<br/>- `requested` | デフォルトブランチの直近のコミット | デフォルトブランチ    |

{% data reusables.developer-site.limit_workflow_to_activity_types %}

このイベントからブランチをフィルタする必要がある場合は、`branches` または `branches-ignore` を使用できます。

この例では、ワークフローは個別の「Run Tests」ワークフローの完了後に実行されるように設定されています。

```yaml
on:
  workflow_run:
    workflows: ["Run Tests"]
    branches: [main]
    types: 
      - completed
      - requested
```

{% endif %}

前回のワークフロー実行の結果に基づいて条件付きでワークフロージョブを実行する場合、[`jobs.<job_id>.if`](/actions/reference/workflow-syntax-for-github-actions#jobsjob_idif) または [`jobs.<job_id>.steps[*].if`](/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsif) 条件を前回の実行の `conclusion` と組み合わせて使用できます。 例:

```yaml
on:
  workflow_run:
    workflows: ["Build"]
    types: [completed]

jobs:
  on-success:
    runs-on: ubuntu-latest
    if: {% raw %}${{ github.event.workflow_run.conclusion == 'success' }}{% endraw %}
    steps:
      ...
  on-failure:
    runs-on: ubuntu-latest
    if: {% raw %}${{ github.event.workflow_run.conclusion == 'failure' }}{% endraw %}
    steps:
      ...
```

## 個人アクセストークンを使った新しいワークフローのトリガー

{% data reusables.github-actions.actions-do-not-trigger-workflows %} 詳しい情報については「[GITHUB_TOKENでの認証](/actions/configuring-and-managing-workflows/authenticating-with-the-github_token)」を参照してください。

ワークフローの実行からワークフローをトリガーしたい場合意は、個人アクセストークンを使ってイベントをトリガーできます。 個人アクセストークンを作成し、それをシークレットとして保存する必要があります。 {% data variables.product.prodname_actions %}の利用コストを最小化するために、再帰的あるいは意図しないワークフローの実行が生じないようにしてください。 個人アクセストークンのシークレットとしての保存に関する詳しい情報については「[暗号化されたシークレットの作成と保存](/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)」を参照してください。
