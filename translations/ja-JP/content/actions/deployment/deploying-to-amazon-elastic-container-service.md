---
title: Amazon Elastic Container Serviceへのデプロイ
intro: 継続的デプロイメント（CD）ワークフローの一部として、Amazon Elastic Container Service (ECS) へのデプロイを行えます。
product: '{% data reusables.gated-features.actions %}'
redirect_from:
  - /actions/guides/deploying-to-amazon-elastic-container-service
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
type: tutorial
topics:
  - CD
  - Containers
  - Amazon ECS
shortTitle: Deploy to Amazon ECS
---

{% data reusables.actions.enterprise-beta %}
{% data reusables.actions.enterprise-github-hosted-runners %}
{% data reusables.actions.ae-beta %}

## はじめに

このガイドは、{% data variables.product.prodname_actions %}を使ってコンテナ化されたアプリケーションをビルドし、それを[Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/)にプッシュし、[Amazon Elastic Container Service (ECS)](https://aws.amazon.com/ecs/)にデプロイする方法を説明します。

{% data variables.product.company_short %}リポジトリへの新しいリリースごとに、{% data variables.product.prodname_actions %}ワークフローは新しいコンテナイメージをビルドし、Amazon ECRにプッシュし、そして新しいタスクの定義をAmazon ECSにデプロイします。

## 必要な環境

{% data variables.product.prodname_actions %}ワークフローを作成する前に、まず以下のAmazon ECR及びECSのセットアップのステップを完了しておかなければなりません。

1. イメージを保存するAmazon ECRリポジトリの作成

   たとえば[AWS CLI](https://aws.amazon.com/cli/)を使って以下を行います。

   {% raw %}```bash{:copy}
   aws ecr create-repository \ --repository-name MY_ECR_REPOSITORY \ --region MY_AWS_REGION
   ```{% endraw %}

   以下のワークフロー中では、`ECR_REPOSITORY`変数に同じAmazon ECRリポジトリ名（ここでは`MY_ECR_REPOSITORY`）を使っていることを確認してください。

   以下のワークフローで `AWS_REGION` 変数（ここでは MY_AWS_REGION`）に同じ AWSリージョンの値を使用していることを確認してください。

2. Amazon ECSのタスク定義、クラスター、サービスの作成

   詳細については、[Getting started wizard on the Amazon ECS console](https://us-east-2.console.aws.amazon.com/ecs/home?region=us-east-2#/firstRun)もしくはAmazon ECSドキュメンテーションの[Fargate を使用した Amazon ECS の開始方法](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/getting-started-fargate.html)を参照してください。

   Amazon ECSサービスとクラスターに設定した名前を記録しておき、以下のワークフロー中で`ECS_SERVICE`及び`ECS_CLUSTER`の変数に使ってください。

3. Amazon ECSのタスク定義をJSONファイルとして{% data variables.product.company_short %}リポジトリに保存してください。

   ファイルのフォーマットは、以下で生成される出力と同じでなければなりません。

   {% raw %}```bash{:copy}
   aws ecs register-task-definition --generate-cli-skeleton
   ```{% endraw %}

以下のワークフローでは、`ECS_TASK_DEFINITION`変数をJSONファイルのパスに設定してください。

   以下のワークフロー中の`CONTAINER_NAME`変数を、タスク定義中の`containerDefinitions`セクションのコンテナ名に設定してください。

4. Amazon IAMアクセスキーの値を保存するための`AWS_ACCESS_KEY_ID`及び`AWS_SECRET_ACCESS_KEY`という名前の{% data variables.product.prodname_actions %}シークレットの作成

   {% data variables.product.prodname_actions %}のシークレットの作成に関する詳しい情報については、「[暗号化されたシークレット]（/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository）」を参照してください。

   IAMユーザに推奨されるIAMポリシー及びアクセスキーの認証情報を処理するメソッドについては、以下で使われている各アクションのドキュメンテーションを参照してください。

## Creating the workflow

Once you've completed the prerequisites, you can proceed with creating the workflow.

以下の例のワークフローは、コンテナイメージを作成してAmazon ECRにプッシュする方法を示します。 そして、タスク定義を新しいイメージIDで更新し、タスク定義をAmazon ECSにデプロイします。

ワークフローの`env`キー内のすべての変数について、自分の値を渡すようにしてください。

```yaml{:copy}
{% data reusables.actions.actions-not-certified-by-github-comment %}

name: Deploy to Amazon ECS

on:
  release:
    types: [ created ]

env:
  AWS_REGION: MY_AWS_REGION                   # set this to your preferred AWS region, e.g. us-west-1
  ECR_REPOSITORY: MY_ECR_REPOSITORY           # set this to your Amazon ECR repository name
  ECS_SERVICE: MY_ECS_SERVICE                 # set this to your Amazon ECS service name
  ECS_CLUSTER: MY_ECS_CLUSTER                 # set this to your Amazon ECS cluster name
  ECS_TASK_DEFINITION: MY_ECS_TASK_DEFINITION # set this to the path to your Amazon ECS task definition
                                               # file, e.g. .aws/task-definition.json
  CONTAINER_NAME: MY_CONTAINER_NAME           # set this to the name of the container in the
                                               # containerDefinitions section of your task definition

defaults:
  run:
    shell: bash

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest{% ifversion fpt or ghes > 3.1 or ghae-next %}
    permissions:
      packages: write
      contents: read{% endif %}

    {% raw %}steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@13d241b293754004c80624b5567555c4a39ffbe3
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@aaf69d68aa3fb14c1d5a6be9ac61fe15b48453a2

      - name: Build, tag, and push image to Amazon ECR
        id: build-image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          # Build a docker container and
          # push it to ECR so that it can
          # be deployed to ECS.
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
          echo "::set-output name=image::$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"

      - name: Fill in the new image ID in the Amazon ECS task definition
        id: task-def
        uses: aws-actions/amazon-ecs-render-task-definition@97587c9d45a4930bf0e3da8dd2feb2a463cf4a3a
        with:
          task-definition: ${{ env.ECS_TASK_DEFINITION }}
          container-name: ${{ env.CONTAINER_NAME }}
          image: ${{ steps.build-image.outputs.image }}

      - name: Deploy Amazon ECS task definition
        uses: aws-actions/amazon-ecs-deploy-task-definition@de0132cf8cdedb79975c6d42b77eb7ea193cf28e
        with:
          task-definition: ${{ steps.task-def.outputs.task-definition }}
          service: ${{ env.ECS_SERVICE }}
          cluster: ${{ env.ECS_CLUSTER }}
          wait-for-service-stability: true{% endraw %}
```


## 追加リソース

この例で使われているサービスに関する詳しい情報については、以下のドキュメンテーションを参照してください。

* Amazon AWSドキュメンテーションの「[IAM でのセキュリティのベストプラクティス](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/best-practices.html)」
* 公式のAWS「[AWS認証情報の設定](https://github.com/aws-actions/configure-aws-credentials)」アクション。
* 公式のAWS [Amazon ECR "ログイン"](https://github.com/aws-actions/amazon-ecr-login)アクション。
* 公式のAWS [Amazon ECS "タスク定義の出力"](https://github.com/aws-actions/amazon-ecs-render-task-definition)アクション。
* 公式のAWS [ Amazon ECS "タスク定義のデプロイ"](https://github.com/aws-actions/amazon-ecs-deploy-task-definition)アクション。
