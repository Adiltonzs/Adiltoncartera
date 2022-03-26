---
title: Personalizar mensagens de usuário para sua empresa
shortTitle: Personalizar mensagens de usuário
redirect_from:
  - /enterprise/admin/user-management/creating-a-custom-sign-in-message/
  - /enterprise/admin/user-management/customizing-user-messages-on-your-instance
  - /admin/user-management/customizing-user-messages-on-your-instance
  - /admin/user-management/customizing-user-messages-for-your-enterprise
intro: 'Você pode criar mensagens personalizadas que os usuários verão em {% data variables.product.product_location %}.'
versions:
  ghes: '*'
  ghae: '*'
type: how_to
topics:
  - Enterprise
  - Maintenance
---

## Sobre mensagens de usuário

Existem vários tipos de mensagens de usuário.
- Mensagens que aparecem na página {% ifversion ghes %}página de ingresso ou {% endif %}saída{% ifversion ghes > 2.22 or ghae %}
- Mandatory messages, which appear once in a pop-up window that must be dismissed{% endif %}{% ifversion ghes or ghae %}
- Banners de anúncios, que aparecem na parte superior de cada página{% endif %}

{% ifversion ghes %}
{% note %}

**Observação:** se você estiver usando SAML para fazer autenticação, a página de login será apresentada pelo seu provedor de identidade e não será possível personalizá-la pelo {% data variables.product.prodname_ghe_server %}.

{% endnote %}

Você pode usar markdown para formatar sua mensagem. Para obter mais informações, consulte "[Sobre gravação e formatação no {% data variables.product.prodname_dotcom %}](/articles/about-writing-and-formatting-on-github/)".

## Criar mensagem personalizada de login

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.settings-tab %}
{% data reusables.enterprise-accounts.messages-tab %}
5. {% ifversion ghes > 2.22 %}À direita de{% else %}em{% endif %} "Página de login", clique **Adicionar mensagem** ou **Editar mensagem**. ![{% ifversion ghes > 2.22 %}Botão de mensagem de Adicionar{% else %}Editar{% endif %}](/assets/images/enterprise/site-admin-settings/edit-message.png)
6. Em **Sign in message** (Mensagem de login), digite a mensagem que você pretende exibir para os usuários. ![Sign in message](/assets/images/enterprise/site-admin-settings/sign-in-message.png){% ifversion ghes > 2.22 %}
{% data reusables.enterprise_site_admin_settings.message-preview-save %}{% else %}
{% data reusables.enterprise_site_admin_settings.click-preview %}
  ![Botão Preview (Visualizar)](/assets/images/enterprise/site-admin-settings/sign-in-message-preview-button.png)
8. Revise a mensagem renderizada. ![Mensagem de login renderizada](/assets/images/enterprise/site-admin-settings/sign-in-message-rendered.png)
{% data reusables.enterprise_site_admin_settings.save-changes %}{% endif %}
{% endif %}

## Criar mensagem personalizada de logout

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.settings-tab %}
{% data reusables.enterprise-accounts.messages-tab %}
5. {% ifversion ghes > 2.22 or ghae %}À direita de{% else %}em{% endif %} "página de logout", clique em **Adicionar mensagem** ou **Editar mensagem**. ![Botão Add message (Adicionar mensagem)](/assets/images/enterprise/site-admin-settings/sign-out-add-message-button.png)
6. Em **Sign out message** (Mensagem de logout), digite a mensagem que você pretende exibir para os usuários. ![Sign two_factor_auth_header message](/assets/images/enterprise/site-admin-settings/sign-out-message.png){% ifversion ghes > 2.22 or ghae %}
{% data reusables.enterprise_site_admin_settings.message-preview-save %}{% else %}
{% data reusables.enterprise_site_admin_settings.click-preview %}
  ![Botão Preview (Visualizar)](/assets/images/enterprise/site-admin-settings/sign-out-message-preview-button.png)
8. Revise a mensagem renderizada. ![Mensagem de logout renderizada](/assets/images/enterprise/site-admin-settings/sign-out-message-rendered.png)
{% data reusables.enterprise_site_admin_settings.save-changes %}{% endif %}

{% ifversion ghes > 2.22 or ghae %}
## Criar uma mensagem obrigatória

Você pode criar uma mensagem obrigatória que {% data variables.product.product_name %} mostrará a todos os usuários na primeira vez que efetuarem o login depois de salvar a mensagem. A mensagem aparece em uma janela pop-up que o usuário deve ignorar antes que o usuário possa usar o {% data variables.product.product_location %}.

As mensagens obrigatórias têm uma série de usos.

- Fornecer informações de integração para novos funcionários
- Contar para os usuários como obter ajuda com {% data variables.product.product_location %}
- Garantir que todos os usuários leiam seus termos de serviço para usar {% data variables.product.product_location %}

Se você incluir caixas de seleção de Markdown na mensagem, todas as caixas de seleção deverão ser selecionadas antes de o usuário poder ignorar a mensagem. Por exemplo, se você incluir seus termos de serviço na mensagem obrigatória, você poderá exigir que cada usuário marque uma caixa de seleção para confirmar que o usuário leu os termos.

Cada vez que um usuário vê uma mensagem obrigatória, um evento de log de auditoria é criado. O evento inclui a versão da mensagem que o usuário visualizou. Para obter mais informações, consulte "[Ações auditadas](/admin/user-management/audited-actions)".

{% note %}

**Observação:** Se você alterar a mensagem obrigatória para {% data variables.product.product_location %}, os usuários que já reconheceram a mensagem não visualizarão a nova mensagem.

{% endnote %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.settings-tab %}
{% data reusables.enterprise-accounts.messages-tab %}
1. À direita da "Mensagem obrigatória", clique em **Adicionar mensagem**. ![Add mandatory message button](/assets/images/enterprise/site-admin-settings/add-mandatory-message-button.png)
1. Em "Mensagem obrigatória", na caixa de texto, digite sua mensagem. ![Mandatory message text box](/assets/images/enterprise/site-admin-settings/mandatory-message-text-box.png)
{% data reusables.enterprise_site_admin_settings.message-preview-save %}

{% endif %}

{% ifversion ghes or ghae %}
## Criar um banner de anúncio global

Você pode definir um banner de anúncio global para ser exibido para todos os usuários na parte superior de cada página.

{% ifversion ghae or ghes > 2.22 %}
Você também pode definir um banner de anúncio{% ifversion ghes %} no shell administrativo usando um utilitário de linha de comando ou{% endif %} usando a API. Para obter mais informações, consulte {% ifversion ghes %}"[Utilitários de linha de comando](/enterprise/admin/configuration/command-line-utilities#ghe-announce)" e {% endif %}"[Administração de {% data variables.product.prodname_enterprise %}](/rest/reference/enterprise-admin#announcements)".
{% else %}

Você também pode definir um banner de anúncio no shell administrativo usando um utilitário de linha de comando. Para obter mais informações, consulte "[Utilitários de linha de comando](/enterprise/admin/configuration/command-line-utilities#ghe-announce)".

{% endif %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.settings-tab %}
{% data reusables.enterprise-accounts.messages-tab %}
1. {% ifversion ghes > 2.22 or ghae %}À direita de{% else %}em{% endif %} "Anúncio", clique em **Adicionar anúncio**. ![Botão Add message (Adicionar mensagem)](/assets/images/enterprise/site-admin-settings/add-announcement-button.png)
1. Em "Anúncio", no campo de texto, digite o anúncio que deseja exibir em um banner. ![Campo de texto para digitar o anúncio](/assets/images/enterprise/site-admin-settings/announcement-text-field.png)
1. Opcionalmente, em "Expira em", selecione o menu suspenso do calendário e clique em uma data de validade. ![Menu suspenso do calendário para escolher data de vencimento](/assets/images/enterprise/site-admin-settings/expiration-drop-down.png)
{% data reusables.enterprise_site_admin_settings.message-preview-save %}
{% endif %}