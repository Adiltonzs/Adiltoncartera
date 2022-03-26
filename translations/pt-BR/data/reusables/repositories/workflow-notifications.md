Se você habilitar o e-mail ou as notificações da web para {% data variables.product.prodname_actions %}, você receberá uma notificação quando qualquer fluxo de trabalho que tenha sido acionado for concluído. A notificação incluirá o status da execução do fluxo de trabalho (incluindo execuções bem-sucedidas, com falhas, neutras e canceladas). Também pode optar por receber uma notificação apenas quando a execução de um fluxo de trabalho falhar.

As notificações de fluxos de trabalho agendados são enviadas ao usuário que inicialmente criou o fluxo de trabalho. Se um usuário diferente atualizar a sintaxe de cron no arquivo de fluxo de trabalho, as notificações subsequentes serão enviadas para esse usuário.{% ifversion fpt or ghes > 2.22 %} Se um fluxo de trabalho programado for desabilitado e depois reabilitado, as notificações serão enviadas ao usuário que reabilitou o fluxo de trabalho em vez de serem enviadas ao usuário que modificou a sintaxe de cron.{% endif %}

Você também pode visualizar o status do fluxo de trabalho executado na aba Ações de um repositório. Para obter mais informações, consulte "[Gerenciar a execução de fluxos de trabalho](/actions/automating-your-workflow-with-github-actions/managing-a-workflow-run)".