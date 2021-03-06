---
title: Filtering and searching issues and pull requests
intro: 'To find detailed information about a repository on {% data variables.product.product_name %}, you can filter, sort, and search issues and pull requests that are relevant to the repository.'
redirect_from:
  - /github/managing-your-work-on-github/finding-information-in-a-repository/filtering-issues-and-pull-requests-by-assignees
  - /articles/filtering-issues-and-pull-requests-by-assignees
  - /github/managing-your-work-on-github/filtering-issues-and-pull-requests-by-assignees
  - /github/managing-your-work-on-github/finding-information-in-a-repository/filtering-issues-and-pull-requests-by-labels
  - /articles/filtering-issues-and-pull-requests-by-labels
  - /github/managing-your-work-on-github/filtering-issues-and-pull-requests-by-labels
  - /github/managing-your-work-on-github/finding-information-in-a-repository/filtering-issues-and-pull-requests
  - /articles/filtering-issues-and-pull-requests
  - /github/managing-your-work-on-github/filtering-issues-and-pull-requests
  - /github/managing-your-work-on-github/finding-information-in-a-repository/filtering-pull-requests-by-review-status
  - /articles/filtering-pull-requests-by-review-status
  - /github/managing-your-work-on-github/filtering-pull-requests-by-review-status
  - /github/managing-your-work-on-github/finding-information-in-a-repository
  - /articles/finding-information-in-a-repository
  - /github/managing-your-work-on-github/finding-information-in-a-repository/sharing-filters
  - /articles/sharing-filters
  - /github/managing-your-work-on-github/sharing-filters
  - /github/managing-your-work-on-github/finding-information-in-a-repository/using-search-to-filter-issues-and-pull-requests
  - /articles/using-search-to-filter-issues-and-pull-requests
  - /github/managing-your-work-on-github/using-search-to-filter-issues-and-pull-requests
  - /github/managing-your-work-on-github/finding-information-in-a-repository/sorting-issues-and-pull-requests
  - /articles/sorting-issues-and-pull-requests
  - /github/managing-your-work-on-github/sorting-issues-and-pull-requests
  - /github/administering-a-repository/finding-information-in-a-repository
  - /github/administering-a-repository/finding-information-in-a-repository/filtering-issues-and-pull-requests
  - /github/administering-a-repository/finding-information-in-a-repository/filtering-issues-and-pull-requests-by-assignees
  - /github/administering-a-repository/finding-information-in-a-repository/filtering-issues-and-pull-requests-by-labels
  - /github/administering-a-repository/finding-information-in-a-repository/filtering-pull-requests-by-review-status
  - /github/administering-a-repository/finding-information-in-a-repository/sorting-issues-and-pull-requests
  - /github/administering-a-repository/finding-information-in-a-repository/using-search-to-filter-issues-and-pull-requests
  - /github/administering-a-repository/finding-information-in-a-repository/sharing-filters
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - Issues
  - Pull requests
shortTitle: Filter and search
---

{% data reusables.cli.filter-issues-and-pull-requests-tip %}

## Filtrar propuestas y solicitudes de extracci??n

Las propuestas y las solicitudes de extracci??n vienen con un conjunto de filtros predeterminados que puedes aplicar para organizar tus listas.

{% data reusables.search.requested_reviews_search %}

Puedes filtrar propuestas y solicitudes de extracci??n para buscar:
- Todas las propuestas y solicitudes de extracci??n abiertas
- Las propuestas y solicitudes de extracci??n creadas por ti
- Las propuestas y solicitudes de extracci??n que se te han asignado
- Las propuestas y solicitudes de extracci??n en las que eres [**@mencionado**](/articles/basic-writing-and-formatting-syntax/#mentioning-people-and-teams)

{% data reusables.cli.filter-issues-and-pull-requests-tip %}

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-issue-pr %}
3. Haz clic en **Filtros** para elegir el tipo de filtro que te interesa. ![Usar el men?? desplegable Filtros](/assets/images/help/issues/issues_filter_dropdown.png)

## Filtrar propuestas y solicitudes de extracci??n por asignatarios

Once you've [assigned an issue or pull request to someone](/articles/assigning-issues-and-pull-requests-to-other-github-users), you can find items based on who's working on them.

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-issue-pr %}
3. En el ??ngulo superior derecho, selecciona el men?? desplegable Asignatario.
4. El men?? desplegable Asignatario menciona a todos los usuarios que tienen acceso de escritura a tu repositorio. Haz clic en el nombre de la persona cuyos elementos asignados deseas ver, o haz clic en **No asignado a nadie** para ver qu?? propuestas no est??n asignadas. ![Utilizar la pesta??a desplegable Asignatarios](/assets/images/help/issues/issues_assignee_dropdown.png)

{% tip %}

Para borrar tu selecci??n de filtro, haz clic en **Borrar consultas de b??squeda, filtros y clasificaciones actuales**.

{% endtip %}

## Filtrar propuestas y solicitudes de extracci??n por etiquetas

Once you've [applied labels to an issue or pull request](/articles/applying-labels-to-issues-and-pull-requests), you can find items based on their labels.

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-issue-pr %}
{% data reusables.project-management.labels %}
4. En la lista de etiquetas, haz clic en una etiqueta para ver las propuestas y solicitudes de extracci??n a las que se ha aplicado. ![Lista de etiquetas de repositorio](/assets/images/help/issues/labels-page.png)

{% tip %}

**Sugerencia:** Para borrar tu selecci??n de filtro, haz clic en **Borrar consultas de b??squeda, filtros y clasificaciones actuales**.

{% endtip %}

## Filtrar solicitudes de extracci??n por estado de revisi??n

Puedes usar filtros para ver en una lista las solicitudes de extracci??n por estado de revisi??n y buscar las solicitudes de extracci??n que has revisado o que otras personas te han pedido que revises.

Puedes filtrar la lista de solicitudes de extracci??n de un repositorio para buscar:
- Solicitudes de extracci??n que no han sido [revisadas](/articles/about-pull-request-reviews) todav??a
- Solicitudes de extracci??n que [requieren una revisi??n](/github/administering-a-repository/about-protected-branches#require-pull-request-reviews-before-merging) antes de que puedan fusionarse
- Solicitudes de extracci??n que ha aprobado un revisor
- Solicitudes de extracci??n en las que un revisor ha pedido cambios
- Solicitudes de extracci??n que t?? has revisado
- Solicitudes de extracci??n que [alguien te ha pedido a ti que revises o a un equipo del que eres miembro](/articles/requesting-a-pull-request-review)

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-pr %}
3. En el ??ngulo superior derecho, selecciona el men?? desplegable Revisiones. ![Men?? desplegable Revisiones en el men?? de filtros sobre la lista de solicitudes de extracci??n](/assets/images/help/pull_requests/reviews-filter-dropdown.png)
4. Elige un filtro para buscar todas las solicitudes de extracci??n con ese estado de filtro. ![Lista de filtros en el men?? desplegable Revisiones](/assets/images/help/pull_requests/pr-review-filters.png)

## Utilizar b??squeda para filtrar propuestas y solicitudes de extracci??n

You can use advanced filters to search for issues and pull requests that meet specific criteria.

### Searching for issues and pull requests

{% include tool-switcher %}

{% webui %}

La barra de b??squeda de propuestas y solicitudes de extracci??n te permite definir tus propios filtros personalizados y clasificar por una amplia variedad de criterios. Puedes encontrar la barra de b??squeda en las pesta??as **Issues** (Propuestas) y **Pull requests** (Solicitudes de extracci??n) de cada repositorio y en tus [tableros de Issues (Propuestas) y Pull requests (Solicitudes de extracci??n)](/articles/viewing-all-of-your-issues-and-pull-requests).

![La barra de b??squeda de propuestas y solicitudes de extracci??n](/assets/images/help/issues/issues_search_bar.png)

{% tip %}

**Sugerencia:** {% data reusables.search.search_issues_and_pull_requests_shortcut %}

{% endtip %}

{% endwebui %}

{% cli %}

{% data reusables.cli.cli-learn-more %}

You can use the {% data variables.product.prodname_cli %} to search for issues or pull requests. Use the `gh issue list` or `gh pr list` subcommand along with the `--search` argument and a search query.

For example, you can list, in order of date created, all issues that have no assignee and that have the label `help wanted` or `bug`.

```shell
gh issue list --search 'no:assignee label:"help wanted",bug sort:created-asc'
```

You can also list all pull requests that mention the `octo-org/octo-team` team.

```shell
gh pr list --search "team:octo-org/octo-team"
```

{% endcli %}

### About search terms

Con los t??rminos de b??squeda de propuestas y solicitudes de extracci??n, puedes hacer lo siguiente:

- Filtrar propuestas y solicitudes de extracci??n por autor: `state:open type:issue author:octocat`
- Filtrar propuestas y solicitudes de extracci??n que involucren, aunque no necesariamente [**@mention**](/articles/basic-writing-and-formatting-syntax/#mentioning-people-and-teams) (mencionen), determinadas personas: `state:open type:issue involves:octocat`
- Filtrar propuestas y solicitudes de extracci??n por asignatario: `state:open type:issue assignee:octocat`
- Filtrar propuestas y solicitudes de extracci??n por etiqueta: `state:open type:issue label:"bug"`
- Filtra los t??rminos de b??squeda utilizando `-` antes del t??rmino: `state:open type:issue -author:octocat`

{% ifversion fpt or ghes > 3.2 or ghae-next %}
{% tip %}

**Tip:** You can filter issues and pull requests by label using logical OR or using logical AND.
- To filter issues using logical OR, use the comma syntax: `label:"bug","wip"`.
- To filter issues using logical AND, use separate label filters: `label:"bug" label:"wip"`.

{% endtip %}
{% endif %}

{% ifversion fpt or ghes or ghae %}
Para el caso de informes de problemas, tambi??n puedes utilizar la b??squeda para:

- Filtrar los informes de problemas enlazados a una solicitud de extracci??n mediante una referencia de cierre: `linked:pr`
{% endif %}

Para las solicitudes de cambios, tambi??n puedes utilizar la b??squeda para:
- Filtrar solicitudes de extracci??n [en borrador](/articles/about-pull-requests#draft-pull-requests): `is:draft`
- Filtrar solicitudes de extracci??n que a??n no hayan sido [revisadas](/articles/about-pull-request-reviews): `state:open type:pr review:none`
- Filtrar solicitudes de extracci??n que [requieran una revisi??n](/github/administering-a-repository/about-protected-branches#require-pull-request-reviews-before-merging) antes de que se puedan fusionar: `state:open type:pr review:required`
- Filtrar solicitudes de extracci??n que haya aprobado un revisor: `state:open type:pr review:approved`
- Filtrar solicitudes de extracci??n en las que un revisor haya solicitado cambios: `state:open type:pr review:changes_requested`
- Filtrar solicitudes de extracci??n por [revisor](/articles/about-pull-request-reviews/): `state:open type:pr reviewed-by:octocat`
- Filtrar solicitudes de extracci??n por el usuario espec??fico [que solicit?? la revisi??n](/articles/requesting-a-pull-request-review): `state:open type:pr review-requested:octocat`
- Filtrar solicitudes de extracci??n por el equipo que se solicita para revisi??n: `state:open type:pr team-review-requested:github/atom`{% ifversion fpt or ghes or ghae %}
- Filtrar por las solicitudes de extracci??n enlazadas con un informe de problemas que se pudiera cerrar con dicha solicitud: `linked:issue`{% endif %}

## Clasificar propuestas y solicitudes de extracci??n

Los filtros pueden ser clasificados para ofrecer mejor informaci??n durante un per??odo de tiempo espec??fico.

Puedes clasificar cualquier vista filtrada por:

* Las propuestas y solicitudes de extracci??n creadas m??s recientemente
* Las propuestas y solicitudes de extracci??n creadas con mayor antig??edad
* Las propuestas y solicitudes de extracci??n m??s comentadas
* Las propuestas y solicitudes de extracci??n menos comentadas
* Las propuestas y solicitudes de extracci??n actualizadas m??s recientemente
* Las propuestas y solicitudes de extracci??n actualizadas con mayor antig??edad
* La reacci??n m??s agregada a las propuestas o solicitudes de cambio

{% data reusables.repositories.navigate-to-repo %}
{% data reusables.repositories.sidebar-issue-pr %}
1. En el ??ngulo superior derecho, selecciona el men?? desplegable de Clasificaci??n. ![Utilizar la pesta??a desplegable de Clasificaci??n](/assets/images/help/issues/issues_sort_dropdown.png)

Para borrar tu selecci??n de clasificaci??n, haz clic en **Sort** &#062 (Clasificar); **Newest** (M??s reciente).


## Compartir filtros

Cuando filtras o clasificas propuestas y solicitudes de extracci??n, la URL de tu navegador se actualiza autom??ticamente para coincidir con la nueva vista.

Puedes enviar la URL que genera esa propuesta a cualquier usuario, que podr?? ver el mismo filtro que t?? ves.

Por ejemplo, si filtras propuestas asignadas a Hubot, y clasificas las propuestas abiertas m??s antiguas, tu URL se actualizar??a a algo similar a esto:

```
/issues?q=state:open+type:issue+assignee:hubot+sort:created-asc
```

## Leer m??s

- "[Searching issues and pull requests](/articles/searching-issues)""
