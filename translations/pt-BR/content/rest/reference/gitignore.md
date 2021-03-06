---
title: Gitignore
intro: The Gitignore API fetches `.gitignore` templates that can be used to ignore files and directories.
redirect_from:
  - /v3/gitignore
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - API
miniTocMaxHeadingLevel: 3
---

Ao criar um novo repositório do {% data variables.product.product_name %} através da API, você pode especificar um [modelo do .gitignore](/github/getting-started-with-github/ignoring-files) a ser aplicado ao repositório após a criação. A API de modlos do .gitignore lista e recupera modelos do repositório de [.gitignore](https://github.com/github/gitignore) de {% data variables.product.product_name %}.

### Tipos de mídia personalizados para gitignore

Você pode usar este tipo de mídia personalizada ao obter um modelo de gitignore.

    application/vnd.github.VERSION.raw

Para obter mais informações, consulte "[Tipos de mídia](/rest/overview/media-types)".

{% include rest_operations_at_current_path %}
