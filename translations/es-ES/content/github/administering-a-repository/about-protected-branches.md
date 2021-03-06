---
title: Acerca de las ramas protegidas
intro: 'You can protect important branches by setting branch protection rules, which define whether collaborators can delete or force push to the branch and set requirements for any pushes to the branch, such as passing status checks or a linear commit history.'
product: '{% data reusables.gated-features.protected-branches %}'
redirect_from:
  - /articles/about-protected-branches
  - /enterprise/admin/developer-workflow/about-protected-branches-and-required-status-checks
  - /articles/about-branch-restrictions
  - /github/administering-a-repository/about-branch-restrictions
  - /articles/about-required-status-checks
  - /github/administering-a-repository/about-required-status-checks
  - /articles/types-of-required-status-checks
  - /github/administering-a-repository/types-of-required-status-checks
  - /articles/about-required-commit-signing
  - /github/administering-a-repository/about-required-commit-signing
  - /articles/about-required-reviews-for-pull-requests
  - /github/administering-a-repository/about-required-reviews-for-pull-requests
versions:
  free-pro-team: '*'
  enterprise-server: '*'
  github-ae: '*'
---

### About branch protection rules

You can enforce certain workflows or requirements before a collaborator can push changes to a branch in your repository, including merging a pull request into the branch, by creating a branch protection rule.

By default, each branch protection rule disables force pushes to the matching branches and prevents the matching branches from being deleted. You can optionally disable these restrictions and enable additional branch protection settings.

By default, the restrictions of a branch protection rule don't apply to people with admin permissions to the repository. You can optionally choose to include administrators, too.

{% data reusables.repositories.branch-rules-example %} For more information about branch name patterns, see "[Managing a branch protection rule](/github/administering-a-repository/managing-a-branch-protection-rule)."

{% data reusables.pull_requests.you-can-auto-merge %}

### About branch protection settings

For each branch protection rule, you can choose to enable or disable the following settings.
- [Require pull request reviews before merging](#require-pull-request-reviews-before-merging)
- [Require status checks before merging](#require-status-checks-before-merging)
- [Requerir confirmaciones firmadas](#require-signed-commits)
- [Require linear history](#require-linear-history)
- [Include administrators](#include-administrators)
- [Restrict who can push to matching branches](#restrict-who-can-push-to-matching-branches)
- [Allow force pushes](#allow-force-pushes)
- [Allow deletions](#allow-deletions)

#### Require pull request reviews before merging

{% data reusables.pull_requests.required-reviews-for-prs-summary %}

If you enable required reviews, collaborators can only push changes to a protected branch via a pull request that is approved by the required number of reviewers with write permissions.

If a person with admin permissions chooses the **Request changes** option in a review, then that person must approve the pull request before the pull request can be merged. If a reviewer who requests changes on a pull request isn't available, anyone with write permissions for the repository can dismiss the blocking review.

{% data reusables.repositories.review-policy-overlapping-commits %}

If a collaborator attempts to merge a pull request with pending or rejected reviews into the protected branch, the collaborator will receive an error message.

```shell
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Changes have been requested.
```

Optionally, you can choose to dismiss stale pull request approvals when commits are pushed. If anyone pushes a commit that modifies code to an approved pull request, the approval will be dismissed, and the pull request cannot be merged. This doesn't apply if the collaborator pushes commits that don't modify code, like merging the base branch into the pull request's branch. Para obtener informaci??n acerca de las ramas base, consulta "[Acerca de las solicitudes de extracci??n](/articles/about-pull-requests)."

Optionally, you can restrict the ability to dismiss pull request reviews to specific people or teams. Para obtener m??s informaci??n, consulta "[Descartar una revisi??n de solicitud de extracci??n](/articles/dismissing-a-pull-request-review)".

Optionally, you can choose to require reviews from code owners. If you do, any pull request that affects code with a code owner must be approved by that code owner before the pull request can be merged into the protected branch.

#### Require status checks before merging

Las verificaciones de estado requeridas garantizan que todas las pruebas de integraci??n continua (CI) requeridas sean aprobadas antes de que los colaboradores puedan realizar cambios en una rama protegida. Para obtener m??s informaci??n, consulta "[Configurar ramas protegidas](/articles/configuring-protected-branches/)" y "[Activar verificaciones de estado requeridas](/articles/enabling-required-status-checks)". Para obtener m??s informaci??n, consulta "[Acerca de las verificaciones de estado ](/github/collaborating-with-issues-and-pull-requests/about-status-checks)".

Antes de que puedas habilitar las verificaciones de estado requeridas, debes configurar el repositorio para utilizar la API de estado. For more information, see "[Repositories](/rest/reference/repos#statuses)" in the REST documentation.

After enabling required status checks, all required status checks must pass before collaborators can merge changes into the protected branch. Una vez que hayan pasado todas las verificaciones de estado requeridas, cualquier confirmaci??n deber?? ya sea subirse en otra rama y despu??s fusionarse, o subirse directo a la rama protegida.

{% note %}

**Nota:** Cualquier persona o integraci??n con permisos de escritura en un repositorio puede establecer el estado de cualquier comprobaci??n de estado en el repositorio. {% data variables.product.company_short %} no verifica que el autor de una comprobaci??n est?? autorizado para crear un determinado nombre o modificar un estado existente. Antes de fusionar una solicitud de extracci??n, deber??s verificar que se est?? esperando al autor de cada estado, los cuales se encuentran listados en la caja de fusi??n.

{% endnote %}

Puedes configurar las verificaciones de estado requeridas para que sean "laxas" o "estrictas". The type of required status check you choose determines whether your branch is required to be up-to-date with the base branch before merging.

| Tipo de verificaci??n de estado requerida | Par??metro                                                                                                                                           | Requisitos de fusi??n                                                          | Consideraciones                                                                                                                                                                                                                                                                                         |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Estricta**                             | La casilla **Require branches to be up-to-date before merging** (Las ramas deben estar actualizadas antes de la fusi??n) est?? marcada.               | La rama **debe** estar actualizada con la rama de base antes de la fusi??n.    | Este es el comportamiento predeterminado para las verificaciones de estado requeridas. Se pueden requerir m??s construcciones, ya que deber??s actualizar la rama de encabezado despu??s de que otros colaboradores fusionen las solicitudes de extracci??n con la rama de base protegida.                  |
| **Flexible**                             | La casilla **Require branches to be up-to-date before merging** (Las ramas deben estar actualizadas antes de la fusi??n) **no** est?? marcada.        | La rama **no debe** estar actualizada con la rama de base antes de la fusi??n. | Tendr??s menos construcciones requeridas, ya que no necesitar??s actualizar la rama de encabezado despu??s de que otros colaboradores fusionen las solicitudes de extracci??n. Las verificaciones de estado pueden fallar despu??s de que fusiones tu rama si hay cambios incompatibles con la rama de base. |
| **Inhabilitada**                         | La casilla **Require status checks to pass before merging** (Se deben superar las verificaciones de estado antes de la fusi??n) **no** est?? marcada. | La rama no tiene restricciones de fusi??n.                                     | Si las verificaciones de estado requeridas no est??n habilitadas, los colaboradores pueden fusionar la rama en cualquier momento, independientemente de si est?? actualizada con la rama de base. Esto aumenta la posibilidad de cambios incompatibles.                                                   |

For troubleshooting information, see "[Troubleshooting required status checks](/github/administering-a-repository/troubleshooting-required-status-checks)."

#### Requerir confirmaciones firmadas

Cuando habilitas el requerir el firmado de confirmaciones en una rama, los colaboradores {% if currentVersion == "free-pro-team@latest" %}y bots{% endif %} solo podr??n subir a la rama aquellas confirmaciones que se hayan firmado y verificado. Para obtener m??s informaci??n, consulta "[Acerca de la verificaci??n de firmas en las confirmaciones](/articles/about-commit-signature-verification)."

{% note %}

**Note:** If a collaborator pushes an unsigned commit to a branch that requires commit signatures, the collaborator will need to rebase the commit to include a verified signature, then force push the rewritten commit to the branch.

{% endnote %}

Siempre puedes subir confirmaciones locales a la rama si estas se firmaron y verificaron. {% if currentVersion == "free-pro-team@latest" %}Tambi??n puedes fusionar las confirmaciones firmadas y verificadas en la rama si utilizas una solicitud de cambios en {% data variables.product.product_name %}. Sin embargo, no puedes combinar y fusionar una solicitud de cambios en la rama en {% data variables.product.product_name %} a menos de que seas el autor de la misma.{% else %} Sin embargo, no puedes fusionar una solicitud de cambios en la rama en {% data variables.product.product_name %}.{% endif %} Puedes {% if currentVersion == "free-pro-team@latest" %}combinar y {% endif %} fusionar solicitudes de cambios localmente. Para obtener m??s informaci??n, consulta la secci??n "[Revisar las solicitudes de extracci??n localmente](/github/collaborating-with-issues-and-pull-requests/checking-out-pull-requests-locally)".

{% if currentVersion == "free-pro-team@latest" %} For more information about merge methods, see "[About merge methods on {% data variables.product.prodname_dotcom %}](/github/administering-a-repository/about-merge-methods-on-github)."{% endif %}

#### Require linear history

Enforcing a linear commit history prevents collaborators from pushing merge commits to the branch. Esto significa que cualquier solicitud de extracci??n fusionada con la rama protegida deber?? utilizar una fusi??n combinada o una fusi??n de rebase. A strictly linear commit history can help teams reverse changes more easily. Para obtener m??s informaci??n acerca de los m??todos de fusi??n, consulta "[Acerca de la fusi??n de solicitudes de extracci??n](/github/collaborating-with-issues-and-pull-requests/about-pull-request-merges)."

Antes de poder requerir un historial de confirmaciones linear, tu repositorio deber?? permitir fusiones combinadas o fusiones de rebase. Para obtener m??s informaci??n, consulta "[Configurar las fusiones de solicitud de extracci??n](/github/administering-a-repository/configuring-pull-request-merges)."

#### Include administrators

By default, protected branch rules do not apply to people with admin permissions to a repository. You can enable this setting to include administrators in your protected branch rules.

#### Restrict who can push to matching branches

{% if currentVersion == "free-pro-team@latest" %}
You can enable branch restrictions if your repository is owned by an organization using
{% data variables.product.prodname_team %} or {% data variables.product.prodname_ghe_cloud %}.
{% endif %}

Cuando habilitas las restricciones de rama, solo los usuarios, equipos o apps a los que se les haya dado permisos pueden subir informaci??n a la rama protegida. Puedes ver y editar los usuarios, equipos o apps con acceso de escritura a una rama protegida en la configuraci??n de la misma.

Solo puedes dar acceso de escritura a una rama protegida para usuarios, equipos o {% data variables.product.prodname_github_apps %} instaladas con acceso de tipo write a un repositorio. Las personas y apps con permisos administrativos en un repositorio siempre pueden subir informaci??n a una rama protegida.

#### Allow force pushes

By default, {% data variables.product.product_name %} blocks force pushes on all protected branches. Cuando habilitas estas subidas forzadas en una rama protegida, cualquiera que tenga privilegios por lo menos de escritura en ese repositorio puede forzar la subida de informaci??n a la rama, incluyendo aquellos con permisos de administrador.

Habilitar las subidas forzadas no invalidar?? ninguna otra regla de protecci??n a la rama. Por ejemplo, si una rama requiere un historial de confirmaciones linear, no puedes forzar la subida de fusi??n de confirmaciones en esa rama.

{% if enterpriseServerVersions contains currentVersion or currentVersion == "github-ae@latest" %}No puedes habilitar las subidas forzadas para una rama protegida si un administrador de sitio las bloque?? en todas las ramas de tu repositorio. Para obtener m??s informaci??n, consulta "[Bloquear las subidas de informaci??n forzadas en los repositorios que sean propiedad de una organizaci??n o cuenta de usuario](/enterprise/{{ currentVersion }}/admin/developer-workflow/blocking-force-pushes-to-repositories-owned-by-a-user-account-or-organization)."

Si un administrador de sitio ha bloqueado las subidas de informaci??n forzadas en la rama predeterminada ??nicamente, entonces a??n puedes habilitarlas en cualquier otra rama protegida.{% endif %}

#### Allow deletions

Por defecto, no puedes eliminar una rama protegida. When you enable deletion of a protected branch, anyone with at least write permissions to the repository can delete the branch.
