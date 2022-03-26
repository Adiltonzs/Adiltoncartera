---
title: Atualizar tokens de acesso do usuário para servidor
intro: 'Para aplicar a rotação regular do token e reduzir o impacto de um token comprometido, você pode configurar seu {% data variables.product.prodname_github_app %} para usar tokens de acesso do usuário expirados.'
redirect_from:
  - /apps/building-github-apps/refreshing-user-to-server-access-tokens
  - /developers/apps/refreshing-user-to-server-access-tokens
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - GitHub Apps
shortTitle: Atualizar acesso do usuário-servidor
---

{% data reusables.pre-release-program.expiring-user-access-tokens %}


## Sobre os tokens de acesso do usuário expirados

Para aplicar a rotação regular do token e reduzir o impacto de um token comprometido, você pode configurar seu {% data variables.product.prodname_github_app %} para usar tokens de acesso do usuário expirados. Para obter mais informações sobre como fazer solicitações de usuário para servidor, consulte "[Identificando e autorizando usuários para aplicativos GitHub](/apps/building-github-apps/identifying-and-authorizing-users-for-github-apps/)".

Os tokens de usuário expiram após 8 horas. Ao receber um novo token de acesso do usuário para servidor, a resposta também conterá um token de atualização, que pode ser trocado por um novo token de usuário e token de atualização. Os tokens de atualização são válidos por 6 meses.

## Renovar um token de usuário com um token de atualização

Para renovar um token de acesso do usuário para servidor, você pode trocar o `refresh_token` por um novo token de acesso e por `refresh_token`.

  `POST https://github.com/login/oauth/access_token`

Esta solicitação de retorno de chamada enviará um novo token de acesso e um novo token de atualização.  Essa solicitação de retorno de chamada é semelhante à solicitação do OAuth que usaria para trocar um `código` temporário por um token de acesso. Para obter mais informações, consulte "[Identificando e autorizando usuários para aplicativos GitHub](/apps/building-github-apps/identifying-and-authorizing-users-for-github-apps/#2-users-are-redirected-back-to-your-site-by-github)" e "[Princípios básicos da autenticação](/rest/guides/basics-of-authentication#providing-a-callback)".

### Parâmetros

| Nome            | Tipo     | Descrição                                                                                                                                                                         |
| --------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `refresh_token` | `string` | **Obrigatório.** O token gerado quando o proprietário do {% data variables.product.prodname_github_app %} habilita tokens expirados e emite um novo token de acesso do usuário. |
| `grant_type`    | `string` | **Obrigatório.** O valor deve ser `refresh_token` (exigido pela especificação do OAuth).                                                                                          |
| `client_id`     | `string` | **Obrigatório.** O ID do cliente para o seu {% data variables.product.prodname_github_app %}.                                                                                   |
| `client_secret` | `string` | **Obrigatório.** O segredo do cliente para o seu {% data variables.product.prodname_github_app %}.                                                                              |

### Resposta

```json
{
  "access_token": "{% ifversion fpt or ghes > 3.1 or ghae-next %}ghu_16C7e42F292c6912E7710c838347Ae178B4a{% else %}e72e16c7e42f292c6912e7710c838347ae178b4a{% endif %}",
  "expires_in": "28800",
  "refresh_token": "{% ifversion fpt or ghes > 3.1 or ghae-next %}ghr_1B4a2e77838347a7E420ce178F2E7c6912E169246c34E1ccbF66C46812d16D5B1A9Dc86A1498{% else %}r1.c1b4a2e77838347a7e420ce178f2e7c6912e169246c34e1ccbf66c46812d16d5b1a9dc86a149873c{% endif %}",
  "refresh_token_expires_in": "15811200",
  "scope": "",
  "token_type": "bearer"
}
```
## Configurar tokens de usuário expirados para um aplicativo GitHub existente

Você pode habilitar ou desabilitar a expiração de tokens de autorização usuário para servidor nas suas configurações do {% data variables.product.prodname_github_app %}.

{% data reusables.user-settings.access_settings %}
{% data reusables.user-settings.developer_settings %}
{% data reusables.user-settings.github_apps %}
4. Clique em **Editar** próximo à sua escolha {% data variables.product.prodname_github_app %}. ![Configurações para edição de um aplicativo GitHub](/assets/images/github-apps/edit-test-app.png)
5. Na barra lateral esquerda, clique em **{% ifversion ghes < 3.1 %} Funcionalidades {% else %} Opcionais {% endif %} de Beta**.
  {% ifversion ghes < 3.1 %} ![Beta features tab](/assets/images/github-apps/beta-features-option.png) {% else %} ![Optional features tab](/assets/images/github-apps/optional-features-option.png) {% endif %}
6. Ao lado de "Expiração do token do usuário para o servidor", clique em **Participar** ou **Não participar**. Esta configuração pode levar alguns segundos para ser aplicada.

## Não participar dos tokens expirados para novos aplicativos do GitHub

Quando você cria um novo {% data variables.product.prodname_github_app %}, por padrão, seu aplicativo usará os tokens de acesso expirados do usuário para servidor.

Se você desejar que o seu aplicativo use tokens de acesso do usuário para servidor que não expiram, você pode desmarcar a opção "Expirar tokens de autorização do usuário" na página de configurações do aplicativo.

![Opção para expirar os tokens dos usuários durante a configuração dos aplicativos GitHub](/assets/images/github-apps/expire-user-tokens-selection.png)

Existing {% data variables.product.prodname_github_apps %} using user-to-server authorization tokens are only affected by this new flow when the app owner enables expiring user tokens for their app.

Enabling expiring user tokens for existing {% data variables.product.prodname_github_apps %} requires sending users through the OAuth flow to re-issue new user tokens that will expire in 8 hours and making a request with the refresh token to get a new access token and refresh token. Para obter mais informações, consulte "[Identificar e autorizar usuários para aplicativos GitHub](/apps/building-github-apps/identifying-and-authorizing-users-for-github-apps/)".

{% ifversion fpt or ghes > 3.1 or ghae-next %}

## Leia mais

- "[Sobre a autenticação em {% data variables.product.prodname_dotcom %}](/github/authenticating-to-github/about-authentication-to-github#githubs-token-formats)"

{% endif %}