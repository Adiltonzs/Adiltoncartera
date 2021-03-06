---
title: GitHub Enterprise 管理
intro: 'You can use these {% data variables.product.prodname_ghe_cloud %} endpoints to administer your enterprise account. Among the tasks you can perform with this API are many relating to GitHub Actions.'
allowTitleToDifferFromFilename: true
redirect_from:
  - /v3/enterprise-admin
  - /v3/enterprise
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - API
miniTocMaxHeadingLevel: 3
shortTitle: 企业管理
---

{% ifversion fpt %}

{% note %}

**注：** 本文章适用于 {% data variables.product.prodname_ghe_cloud %}。 要查看 {% data variables.product.prodname_ghe_managed %} 或 {% data variables.product.prodname_ghe_server %} 版本，请使用 **{% data ui.pages.article_version %}** 下拉菜单。

{% endnote %}

{% endif %}

### 端点 URL

REST API 端点{% ifversion ghes %}— [管理控制台](#management-console) API 端点除外—{% endif %} 是以下 URL 的前缀：

```shell
{% data variables.product.api_url_pre %}
```

{% ifversion ghes %}
[管理控制台](#management-console) API 端点是唯一以主机名为前缀的端点：

```shell
http(s)://<em>hostname</em>/
```
{% endif %}
{% ifversion ghae or ghes %}
### 身份验证

{% data variables.product.product_name %} 安装设施的 API 端点接受与 GitHub.com [相同的身份验证方法](/rest/overview/resources-in-the-rest-api#authentication)。 您可以使用 **[OAuth 令牌](/apps/building-integrations/setting-up-and-registering-oauth-apps/)**{% ifversion ghes %}（可使用[授权 API](/rest/reference/oauth-authorizations#create-a-new-authorization) 创建）{% endif %}或**[基本身份验证](/rest/overview/resources-in-the-rest-api#basic-authentication)**来验证自己。 {% ifversion ghes %} OAuth 令牌用于企业特定的端点时必须具有 `site_admin` [OAuth 作用域](/developers/apps/scopes-for-oauth-apps#available-scopes)。{% endif %}

企业管理 API 端点只有经过身份验证的 {% data variables.product.product_name %} 站点管理员可以访问{% ifversion ghes %}，但[管理控制台](#management-console) API 例外，它需要[管理控制台密码](/enterprise/admin/articles/accessing-the-management-console/){% endif %}。

{% endif %}

{% ifversion ghae or ghes %}
### 版本信息

每个 API 的响应标头中都会返回企业的当前版本：`X-GitHub-Enterprise-Version: {{currentVersion}}.0` 您也可以通过调用[元端点](/rest/reference/meta/)来读取当前版本。

{% for operation in currentRestOperations %}
  {% unless operation.subcategory %}{% include rest_operation %}{% endunless %}
{% endfor %}

{% endif %}

{% ifversion fpt %}

## 审核日志

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'audit-log' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion fpt %}
## 计费

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'billing' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

## GitHub Actions

{% data reusables.actions.ae-beta %}

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'actions' %}{% include rest_operation %}{% endif %}
{% endfor %}


{% ifversion ghae or ghes %}
## 管理统计

管理统计 API 提供有关安装设施的各种指标。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'admin-stats' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghae or ghes > 2.22 %}

## 公告

公告 API 允许您管理企业中的全局公告横幅。 更多信息请参阅“[自定义企业的用户消息](/admin/user-management/customizing-user-messages-for-your-enterprise#creating-a-global-announcement-banner)”。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'announcement' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghae or ghes %}

## 全局 web 挂钩

全局 web 挂钩安装在企业上。 您可以使用全局 web 挂钩来自动监视、响应或实施针对企业上的用户、组织、团队和仓库的规则。 全局 web 挂钩可以订阅[组织](/developers/webhooks-and-events/webhook-events-and-payloads#organization)、[用户](/developers/webhooks-and-events/webhook-events-and-payloads#user)、[仓库](/developers/webhooks-and-events/webhook-events-and-payloads#repository)、[团队](/developers/webhooks-and-events/webhook-events-and-payloads#team)、[成员](/developers/webhooks-and-events/webhook-events-and-payloads#member)、[成员身份](/developers/webhooks-and-events/webhook-events-and-payloads#membership)、[复刻](/developers/webhooks-and-events/webhook-events-and-payloads#fork)和 [ping](/developers/webhooks-and-events/about-webhooks#ping-event) 事件类型。

*此 API 只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。 要了解如何配置全局 web 挂钩，请参阅[关于全局 web 挂钩](/enterprise/admin/user-management/about-global-webhooks)。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'global-webhooks' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}

## LDAP

您可以使用 LDAP API 来更新 {% data variables.product.product_name %} 用户或团队与其关联的 LDAP 条目之间的帐户关系，或者排队新同步。

通过 LDAP 映射端点，您可以更新用户或团队所映射的识别名称 (DN) 。 请注意，LDAP 端点通常只在您的 {% data variables.product.product_name %} 设备[启用了 LDAP 同步](/enterprise/admin/authentication/using-ldap)时才有效。 启用了 LDAP 后，即使禁用 LDAP 同步，也可以使用[更新用户的 LDAP 映射](#update-ldap-mapping-for-a-user)端点。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'ldap' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghae or ghes %}
## 许可

许可 API 提供有关企业许可的信息。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'license' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}

## 管理控制台

管理控制台 API 可帮助您管理 {% data variables.product.product_name %} 安装设施。

{% tip %}

在对管理控制台进行 API 调用时，必须明确设置端口号。 如果在企业上启用了 TLS，则端口号为 `8443`；否则，端口号为 `8080`。

如果您不想提供端口号，则需要将工具配置为自动遵循重定向。

使用 `curl` 时，您可能还需要添加 [`-k` 标志](http://curl.haxx.se/docs/manpage.html#-k)，因为 {% data variables.product.product_name %} 在您[添加自己的 TLS 证书](/enterprise/admin/guides/installation/configuring-tls/)之前会使用自签名证书。

{% endtip %}

### 身份验证

您需要将[管理控制台密码](/enterprise/admin/articles/accessing-the-management-console/)作为身份验证令牌传递给除 [`/setup/api/start`](#create-a-github-enterprise-server-license) 之外的每个管理控制台 API 端点。

使用 `api_key` 参数在每个请求中发送此令牌。 例如：

```shell
$ curl -L 'https://<em>hostname</em>:<em>admin_port</em>/setup/api?api_key=<em>your-amazing-password</em>'
```

还可以使用标准 HTTP 身份验证发送此令牌。 例如：

```shell
$ curl -L 'https://api_key:<em>your-amazing-password</em>@<em>hostname</em>:<em>admin_port</em>/setup/api'
```

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'management-console' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghae or ghes %}
## 组织

组织管理 API 允许您在企业上创建组织。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'orgs' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}
## 组织预接收挂钩

组织预接收挂钩 API 允许您查看和修改组织可用的预接收挂钩的实施。

### 对象属性

| 名称                               | 类型    | 描述           |
| -------------------------------- | ----- | ------------ |
| `name`                           | `字符串` | 挂钩的名称。       |
| `enforcement`                    | `字符串` | 此仓库中挂钩的实施状态。 |
| `allow_downstream_configuration` | `布尔值` | 仓库是否可以覆盖实施。  |
| `configuration_url`              | `字符串` | 设置实施的端点 URL。 |

*enforcement* 的可能值包括 `enabled`、`disabled` 和 `testing`。 `disabled` 表示预接收挂钩不会运行。 `enabled` 表示它将运行并拒绝会导致非零状态的任何推送。 `testing` 表示脚本将运行，但不会导致任何推送被拒绝。

`configuration_url` 可能是此端点或此挂钩的全局配置的链接。 只有站点管理员才能访问全局配置。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'org-pre-receive-hooks' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}

## 预接收环境

预接收环境 API 允许您创建、列出、更新和删除预接收挂钩的环境。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。

### 对象属性

#### 预接收环境

| 名称                    | 类型    | 描述                                                      |
| --------------------- | ----- | ------------------------------------------------------- |
| `name`                | `字符串` | UI 中显示的环境名称。                                            |
| `image_url`           | `字符串` | 将要下载并解压缩的 tarball 的 URL。                                |
| `default_environment` | `布尔值` | 这是否是 {% data variables.product.product_name %} 附带的默认环境。 |
| `download`            | `对象`  | 此环境的下载状态。                                               |
| `hooks_count`         | `整数`  | 使用此环境的预接收挂钩数量。                                          |

#### 预接收环境下载

| 名称              | 类型    | 描述            |
| --------------- | ----- | ------------- |
| `state`         | `字符串` | 最近下载的状态。      |
| `downloaded_at` | `字符串` | 最近下载开始的时间。    |
| `message`       | `字符串` | 在失败时生成任何错误消息。 |

`state` 的可能值包括 `not_started`、`in_progress`、`success`、`failed`。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'pre-receive-environments' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}
## 预接收挂钩

预接收挂钩 API 允许您创建、列出、更新和删除预接收挂钩。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `404` 响应。

### 对象属性

#### 预接收挂钩

| 名称                               | 类型    | 描述                 |
| -------------------------------- | ----- | ------------------ |
| `name`                           | `字符串` | 挂钩的名称。             |
| `script`                         | `字符串` | 挂钩运行的脚本。           |
| `script_repository`              | `对象`  | 保存脚本的 GitHub 仓库。   |
| `environment`                    | `对象`  | 执行脚本的预接收环境。        |
| `enforcement`                    | `字符串` | 此挂钩的实施状态。          |
| `allow_downstream_configuration` | `布尔值` | 是否可以在组织或仓库级别上覆盖实施。 |

*enforcement* 的可能值包括 `enabled`、`disabled` 和 `testing`。 `disabled` 表示预接收挂钩不会运行。 `enabled` 表示它将运行并拒绝会导致非零状态的任何推送。 `testing` 表示脚本将运行，但不会导致任何推送被拒绝。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'pre-receive-hooks' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghes %}

## 仓库预接收挂钩

仓库预接收挂钩 API 允许您查看和修改仓库可用的预接收挂钩的实施。

### 对象属性

| 名称                  | 类型    | 描述           |
| ------------------- | ----- | ------------ |
| `name`              | `字符串` | 挂钩的名称。       |
| `enforcement`       | `字符串` | 此仓库中挂钩的实施状态。 |
| `configuration_url` | `字符串` | 设置实施的端点 URL。 |

*enforcement* 的可能值包括 `enabled`、`disabled` 和 `testing`。 `disabled` 表示预接收挂钩不会运行。 `enabled` 表示它将运行并拒绝会导致非零状态的任何推送。 `testing` 表示脚本将运行，但不会导致任何推送被拒绝。

`configuration_url` 可能是此仓库、其组织所有者或全局配置的链接。 `configuration_url` 端点的访问授权在所有者或站点管理员级别确定。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'repo-pre-receive-hooks' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}

{% ifversion ghae or ghes %}
## 用户

用户管理 API 允许您暂停{% ifversion ghes %}、取消暂停、升级和降级{% endif %}{% ifversion ghae %} 以及取消暂停{% endif %} 企业上的用户。 *它只适用于[经过身份验证的](/rest/overview/resources-in-the-rest-api#authentication)站点管理员。*普通用户尝试访问它时会收到 `403` 响应。

{% for operation in currentRestOperations %}
  {% if operation.subcategory == 'users' %}{% include rest_operation %}{% endif %}
{% endfor %}

{% endif %}
