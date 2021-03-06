---
title: Solucionar problemas en el flujo de trabajo de CodeQL
shortTitle: Solucionar problemas del flujo de trabajo de CodeQL
intro: 'Si tienes problemas con el {% data variables.product.prodname_code_scanning %}, puedes solucionarlos si utilizas estos tips para resolver estos asuntos.'
product: '{% data reusables.gated-features.code-scanning %}'
redirect_from:
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/troubleshooting-code-scanning
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/troubleshooting-the-codeql-workflow
  - /code-security/secure-coding/troubleshooting-the-codeql-workflow
  - /code-security/secure-coding/automatically-scanning-your-code-for-vulnerabilities-and-errors/troubleshooting-the-codeql-workflow
versions:
  fpt: '*'
  ghes: '>=3.0'
  ghae: '*'
type: how_to
topics:
  - Advanced Security
  - Code scanning
  - CodeQL
  - Actions
  - Troubleshooting
  - Repositories
  - Pull requests
  - C/C++
  - C#
  - Java
---

<!--For this article in earlier GHES versions, see /content/github/finding-security-vulnerabilities-and-errors-in-your-code-->

{% data reusables.code-scanning.beta %}
{% data reusables.code-scanning.not-available %}

## Producir bitácoras detalladas para la depuración

Para producir una salida más detallada de bitácoras, puedes habilitar el registro de depuración de pasos. Para obtener más información, consulta la sección "[Habilitar el registro de depuración](/actions/managing-workflow-runs/enabling-debug-logging#enabling-step-debug-logging)."

## Compilación automática para los fallos de un lenguaje compilado

Si una compilación automática de código para un lenguaje compilado dentro de tu proyecto falla, intenta los siguientes pasos de solución de problemas.

- Elimina el paso de `autobuild` de tu flujo de trabajo de {% data variables.product.prodname_code_scanning %} y agrega los pasos de compilación específicos. Para obtener información sobre cómo editar el flujo de trabajo, consulta la sección "[Configurar el {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/configuring-code-scanning#editing-a-code-scanning-workflow)". Para obtener más información sobre cómo reemplazar el paso de `autobuild`, consulta la sección "[Configurar el flujo de trabajo de {% data variables.product.prodname_codeql %} para los lenguajes compilados](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".

- Si tu flujo de trabajo no especifica explícitamente los lenguajes a analizar, {% data variables.product.prodname_codeql %} detectará implícitamente los lenguajes compatibles en tu código base. En esta configuración, fuera de los lenguajes compilados C/C++, C#, y Java, {% data variables.product.prodname_codeql %} solo analizará el lenguaje presente en la mayoría de los archivos de origen. Edita el flujo de trabajo y agrega una matriz de compilación que especifique los lenguajes que quieres analizar. El flujo de análisis predeterminado de CodeQL utiliza dicha matriz.

  Los siguientes extractos de un flujo de trabajo te muestran cómo puedes utilizar una matriz dentro de la estrategia del job para especificar lenguajes, y luego hace referencia a cada uno de ellos con el paso de "Inicializar {% data variables.product.prodname_codeql %}":

  ```yaml
  jobs:
    analyze:{% ifversion fpt or ghes > 3.1 or ghae-next %}
      permissions:
        security-events: write
        actions: read{% endif %}
      ...
      strategy:
        fail-fast: false
        matrix:
          language: ['csharp', 'cpp', 'javascript']

      steps:
      ...
        - name: Initialize {% data variables.product.prodname_codeql %}
          uses: github/codeql-action/init@v1
          with:
            languages: {% raw %}${{ matrix.language }}{% endraw %}
  ```

  Para obtener más información acerca de editar el flujo de trabajo, consulta la sección "[Configurar el escaneo de código](/code-security/secure-coding/configuring-code-scanning)".

## No se encontró código durante la compilación

Si tu flujo de trabajo falla con un error de `No source code was seen during the build` o de `The process '/opt/hostedtoolcache/CodeQL/0.0.0-20200630/x64/codeql/codeql' failed with exit code 32`, esto indica que {% data variables.product.prodname_codeql %} no pudo monitorear tu código. Hay muchas razones que podrían explicar esta falla:

1. La detección automática del lenguaje identificó un lenguaje compatible, pero no hay código analizable en dicho lenguaje dentro del repositorio. Un ejemplo típico es cuando nuestro servicio de detección de lenguaje encuentra un archivo que se asocia con un lenguaje de programación específico como un archivo `.h`, o `.gyp`, pero no existe el código ejecutable correspondiente a dicho lenguaje en el repositorio. Para resolver el problema, puedes definir manualmente los lenguajes que quieras analizar si actualizas la lista de éstos en la matriz de `language`. Por ejemplo, la siguiente configuración analizará únicamente a Go y a Javascript.

  ```yaml
  strategy:
    fail-fast: false
    matrix:
      # Override automatic language detection by changing the list below
      # Supported options are:
      # ['csharp', 'cpp', 'go', 'java', 'javascript', 'python']
      language: ['go', 'javascript']
  ```
Para obtener más información, consulta el extracto de flujo de trabajo en la sección "[Compilación automática para los fallos de un lenguaje compilado](#automatic-build-for-a-compiled-language-fails)" que se trata anteriormente.
1. Tu flujo de trabajo de {% data variables.product.prodname_code_scanning %} está analizando un lenguaje compilado (C, C++, C#, o Java), pero el código no se compiló. Predeterminadamente, el flujo de trabajo de análisis de {% data variables.product.prodname_codeql %} contiene un paso de `autobuild`, sin embargo, este paso representa un proceso de mejor esfuerzo y podría no tener éxito en compilar tu código, dependiendo de tu ambiente de compilación específico. También podría fallar la compilación si eliminaste el paso de `autobuild` y no incluiste manualmente los pasos de compilación.  Para obtener más información acerca de especificar los pasos de compilación, consulta la sección "[Configurar el flujo de trabajo de {% data variables.product.prodname_codeql %} para los lenguajes compilados](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".
1. Tu flujo de trabajo está analizando un lenguaje compilado (C, C++, C#, or Java), pero algunas porciones de tu compilación se almacenan en caché para mejorar el rendimiento (esto muy probablemente ocurra con los sistemas de compilación como Gradle o Bazel). Ya que {% data variables.product.prodname_codeql %} observa la actividad del compilador para entender los flujos de datos en un repositorio, {% data variables.product.prodname_codeql %} requiere que suceda una compilación completa para poder llevar a cabo este análisis.
1. Tu flujo de trabajo está analizando un lenguaje compilado (C, C++, C#, or Java), pero no ocurre ninguna compilación entre los pasos de `init` y `analyze` en el flujo de trabajo. {% data variables.product.prodname_codeql %} requiere que tu compilación tome lugar entre estos dos pasos para poder observar la actividad del compilador y llevar a cabo el análisis.
1. Tu código compilado (en C, C++, C#, or Java) se compiló con éxito, pero {% data variables.product.prodname_codeql %} no pudo detectar las invocaciones del compilador. Las causas más comunes son:

   * Ejecutar tu proceso de compilación en un contenedor separado a {% data variables.product.prodname_codeql %}. Para obtener más información, consulta la sección "[Ejecutar el escaneo de código de CodeQL en un contenedor](/code-security/secure-coding/running-codeql-code-scanning-in-a-container)".
   * Compilar utilizando un sistema de compilación distribuida externo a GitHub Actions, utilizando un proceso de daemon.
   * {% data variables.product.prodname_codeql %} no está consciente del compilador específico que estás utilizando.

  Para los proyectos de .NET Framework y los de C# que utilicen ya sea `dotnet build` o `msbuild` y que apunten a .NET Core 2, debes especificar `/p:UseSharedCompilation=false` en el paso `run` de tu flujo de trabajo cuando compiles tu código. No es necesario el marcador `UseSharedCompilation` para .NET Core 3.0 o superior.

  Por ejemplo, la siguiente configuración para C# pasará el marcador durante el primer paso de compilación.

   ``` yaml
   - run: |
       dotnet build /p:UseSharedCompilation=false 
   ```

  Si encuentras otro problema con tu compilador específico o con tu configuración, contacta a {% data variables.contact.contact_support %}.

Para obtener más información acerca de especificar los pasos de compilación, consulta la sección "[Configurar el flujo de trabajo de {% data variables.product.prodname_codeql %} para los lenguajes compilados](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".

{% ifversion fpt or ghes > 3.1  or ghae-next %}
## Lines of code scanned are lower than expected

For compiled languages like C/C++, C#, Go, and Java, {% data variables.product.prodname_codeql %} only scans files that are built during the analysis. Therefore the number of lines of code scanned will be lower than expected if some of the source code isn't compiled correctly. This can happen for several reasons:

1. The {% data variables.product.prodname_codeql %} `autobuild` feature uses heuristics to build the code in a repository. However, sometimes this approach results in an incomplete analysis of a repository. For example, when multiple `build.sh` commands exist in a single repository, the analysis may not be complete since the `autobuild` step will only execute one of the commands, and therefore some source files may not be compiled.
1. Some compilers do not work with {% data variables.product.prodname_codeql %} and can cause issues while analyzing the code. For example, Project Lombok uses non-public compiler APIs to modify compiler behavior. The assumptions used in these compiler modifications are not valid for {% data variables.product.prodname_codeql %}'s Java extractor, so the code cannot be analyzed.

If your {% data variables.product.prodname_codeql %} analysis scans fewer lines of code than expected, there are several approaches you can try to make sure all the necessary source files are compiled.

### Replace the `autobuild` step

Replace the `autobuild` step with the same build commands you would use in production. This makes sure that {% data variables.product.prodname_codeql %} knows exactly how to compile all of the source files you want to scan. Para obtener más información, consulta la sección "[Configurar el flujo de trabajo de {% data variables.product.prodname_codeql %} para los lenguajes compilados](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".

### Inspect the copy of the source files in the {% data variables.product.prodname_codeql %} database
You may be able to understand why some source files haven't been analyzed by inspecting the copy of the source code included with the {% data variables.product.prodname_codeql %} database. To obtain the database from your Actions workflow, add an `upload-artifact` action after the analysis step in your code scanning workflow:
```
- uses: actions/upload-artifact@v2
  with:
    name: codeql-database
    path: ../codeql-database
```
This uploads the database as an actions artifact that you can download to your local machine. For more information, see "[Storing workflow artifacts](/actions/guides/storing-workflow-data-as-artifacts)."

The artifact will contain an archived copy of the source files scanned by {% data variables.product.prodname_codeql %} called _src.zip_. If you compare the source code files in the repository and the files in _src.zip_, you can see which types of file are missing. Once you know what types of file are not being analyzed, it is easier to understand how you may need to change the workflow for {% data variables.product.prodname_codeql %} analysis.

## Extraction errors in the database

The {% data variables.product.prodname_codeql %} team constantly works on critical extraction errors to make sure that all source files can be scanned. However, the {% data variables.product.prodname_codeql %} extractors do occasionally generate errors during database creation. {% data variables.product.prodname_codeql %} provides information about extraction errors and warnings generated during database creation in a log file. The extraction diagnostics information gives an indication of overall database health. Most extractor errors do not significantly impact the analysis. A small number of extractor errors is healthy and typically indicates a good state of analysis.

However, if you see extractor errors in the overwhelming majority of files that were compiled during database creation, you should look into the errors in more detail to try to understand why some source files weren't extracted properly.

{% else %}
## Algunas porciones de mi repositorio no se analizaron con `autobuild`

La característica de `autobuild` de {% data variables.product.prodname_codeql %} utiliza la heurística para compilar el código en un repositorio, sin embargo, algunas veces este acercamiento da como resultado un análisis incompleto de un repositorio. Por ejemplo, cuando existen comandos múltiples de `build.sh` en un solo repositorio, el análisis podría no completarse, ya que el paso de `autobuild` solo se ejecutará en uno de los comandos. La solución es reemplazar el paso de `autobuild` con los pasos de compilación que compilarán todo el código fuente que quieras analizar. Para obtener más información, consulta la sección "[Configurar el flujo de trabajo de {% data variables.product.prodname_codeql %} para los lenguajes compilados](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".
{% endif %}


## La compilación tarda demasiado

Si tu compilación con análisis de {% data variables.product.prodname_codeql %} toma demasiado para ejecutarse, hay varios acercamientos que puedes intentar para reducir el tiempo de compilación.

### Incrementar la memoria o los núcleos

Si utilizas ejecutores auto-hospedados para ejecutar el análisis de {% data variables.product.prodname_codeql %}, puedes incrementar la memoria o la cantidad de núcleos en estos ejecutores.

### Utilizar matrices de compilación para paralelizar el análisis

El {% data variables.product.prodname_codeql_workflow %} predeterminado utiliza una matriz de lenguajes, la cual causa que el análisis de cada uno de ellos se ejecute en paralelo. Si especificaste los lenguajes que quieres analizar directamente en el paso de "Inicializar CodeQL", el análisis de cada lenguaje ocurrirá de forma secuencial. Para agilizar el análisis de lenguajes múltiples, modifica tu flujo de trabajo para utilizar una matriz. Para obtener más información, consulta el extracto de flujo de trabajo en la sección "[Compilación automática para los fallos de un lenguaje compilado](#automatic-build-for-a-compiled-language-fails)" que se trata anteriormente.

### Reducir la cantidad de código que se está analizando en un solo flujo de trabajo

El tiempo de análisis es habitualmente proporcional a la cantidad de código que se esté analizando. Puedes reducir el tiempo de análisis si reduces la cantidad de código que se analice en cada vez, por ejemplo, si excluyes el código de prueba o si divides el análisis en varios flujos de trabajo que analizan únicamente un subconjunto de tu código a la vez.

Para los lenguajes compilados como Java, C, C++ y C#, {% data variables.product.prodname_codeql %} analiza todo el código que se haya compilado durante la ejecución del flujo de trabajo. Para limitar la cantidad de código que se está analizando, compila únicamente el código que quieres analizar especificando tus propios pasos de compilación en un bloque de `run`. Puedes combinar el especificar tus propios pasos de compilación con el uso de filtros de `paths` o `paths-ignore` en los eventos de `pull_request` y de `push` para garantizar que tu flujo de trabajo solo se ejecute cuando se cambia el código específico. Para obtener más información, consulta la sección "[Sintaxis de flujo de trabajo para {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths)".

Para los lenguajes interpretados como Go, JavaScript, Python y TypeScript que analiza {% data variables.product.prodname_codeql %} sin una compilación específica, puedes especificar opciones de configuración adicionales para limitar la cantidad de código a analizar. Para obtener más información, consulta la sección "[Especificar los directorios a escanear](/code-security/secure-coding/configuring-code-scanning#specifying-directories-to-scan)".

Si divides tu análisis en varios flujos de trabajo como se describió anteriormente, aún te recomendamos que por lo menos tengas un flujo de trabajo que se ejecute con un `schedule` que analice todo el código en tu repositorio. Ya que {% data variables.product.prodname_codeql %} analiza los flujos de datos entre componentes, algunos comportamientos de seguridad complejos podrían detectarse únicamente en una compilación completa.

### Ejecutar únicamente durante un evento de `schedule`

Si tu análisis aún es muy lento como para ejecutarse durante eventos de `push` o de `pull_request`, entonces tal vez quieras activar el análisis únicamente en el evento de `schedule`. Para obtener más información, consulta la sección "[Eventos](/actions/learn-github-actions/introduction-to-github-actions#events)".

{% ifversion fpt %}
## Los resultados difieren de acuerdo con la plataforma de análisis

Si estás analizando código escrito en Python, podrías ver resultados diferentes dependiendo de si ejecutas el {% data variables.product.prodname_codeql_workflow %} en Linux, macOS o Windows.

En los ejecutores hospedados en GitHub que utilizan Linux, el {% data variables.product.prodname_codeql_workflow %} intenta instalar y analizar las dependencias de Python, lo cual podría llevar a más resultados. Para inhabilitar la auto instalación, agrega `setup-python-dependencies: false` al paso de "Initialize CodeQL" en el flujo de trabajo. Para obtener más información acerca de la configuración del análisis para las dependencias de Python, consulta la sección "[Analizar las dependencias de Python](/code-security/secure-coding/configuring-code-scanning#analyzing-python-dependencies)".

{% endif %}

## Error: "Error de servidor"

Si la ejecución de un flujo de trabajo para {% data variables.product.prodname_code_scanning %} falla debido a un error de servidor, trata de ejecutar el flujo de trabajo nuevamente. Si el problema persiste, contaca a {% data variables.contact.contact_support %}.

## Error: "Out of disk" o "Out of memory"

On very large projects, {% data variables.product.prodname_codeql %} may run out of disk or memory on the hosted {% data variables.product.prodname_actions %} runner.
{% ifversion fpt %}Si te encuentras con este problema en un ejecutor de {% data variables.product.prodname_actions %}, contacta a {% data variables.contact.contact_support %} para que podamos investigar el problema.
{% else %}Si llegas a tener este problema, intenta incrementar la memoria en el ejecutor.{% endif %}

{% ifversion fpt %}
## Error:403 "No se puede acceder al recurso por integración" al utilizar {% data variables.product.prodname_dependabot %}

El {% data variables.product.prodname_dependabot %} se considera no confiable cuando activa una ejecución de flujo de trabajo y este se ejecutará con un alcance de solo lectura. El cargar resultados del {% data variables.product.prodname_code_scanning %} en una rama a menudo requiere del alcance `security_events: write`. Sin embargo, el {% data variables.product.prodname_code_scanning %} siempre permite que se carguen los resultados cuando el evento `pull_request` activa la ejecución de la acción. Es por esto que, para las ramas del {% data variables.product.prodname_dependabot %}, te recomendamos utilizar el evento `pull_request` en vez del evento `push`.

Un enfoque simple es ejecutar las subidas a la rama predeterminada y cualquier otra rama que se ejecute a la larga, así como las solicitudes de cambio que se abren contra este conjunto de ramas:
```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
```
Un enfoque alternativo es ejecutar todas las subidas con excepción de las ramas del {% data variables.product.prodname_dependabot %}:
```yaml
on:
  push:
    branches-ignore:
      - 'dependabot/**'
  pull_request:
```

### El análisis aún falla en la rama predeterminada

Si el {% data variables.product.prodname_codeql_workflow %} aún falla en una confirmación que se hizo en una rama predeterminada, debes verificar:
- si el {% data variables.product.prodname_dependabot %} fue el autor de la confirmación
- si la solicitud de cambios que incluye la confirmación se fusionó utilizando `@dependabot squash and merge`

Este tipo de confirmación por fusión tiene como autor al {% data variables.product.prodname_dependabot %} y, por lo tanto, cualquier flujo de trabajo que se ejecute en la confirmación tendrá permisos de solo lectura. Si habilitaste las actualizaciones de seguridad o de versión del {% data variables.product.prodname_code_scanning %} y del {% data variables.product.prodname_dependabot %} en tu repositorio, te recomendamos que evites utilizar el comando `@dependabot squash and merge` del {% data variables.product.prodname_dependabot %}. En su lugar, puedes habilitar la fusión automática en tu repositorio. Esto significa que las solicitudes de cambio se fusionarán automáticamente cuando se cumplan todas las revisiones requeridas y cuando pasen todas las verificaciones de estado. Para obtener más información sobre cómo habilitar la fusión automática, consulta la sección "[Fusionar una solicitud de cambios automáticamente](/github/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request#enabling-auto-merge)".
{% endif %}

## Warning: "git checkout HEAD^2 is no longer necessary"

Si estás utilizando un flujo de trabajo de {% data variables.product.prodname_codeql %} antiguo, podrías obtener el siguiente mensaje de advertencia en la salida "inicializar {% data variables.product.prodname_codeql %}" de la acción:

```
Warning: 1 issue was detected with this workflow: git checkout HEAD^2 is no longer 
necessary. Please remove this step as Code Scanning recommends analyzing the merge 
commit for best results.
```

Puedes arreglar esto si eliminas las siguientes líneas del flujo de trabajo de {% data variables.product.prodname_codeql %}. Estas líneas se incluyeron en la sección de `steps` del job `Analyze` en las versiones iniciales del flujo de trabajo de {% data variables.product.prodname_codeql %}.

```yaml
        with:
          # We must fetch at least the immediate parents so that if this is
          # a pull request then we can checkout the head.
          fetch-depth: 2

      # If this run was triggered by a pull request event, then checkout
      # the head of the pull request instead of the merge commit.
      - run: git checkout HEAD^2
        if: {% raw %}${{ github.event_name == 'pull_request' }}{% endraw %}
```

La sección revisada de `steps` en el flujo de trabajo se deberá ver así:

```yaml
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      # Initializes the {% data variables.product.prodname_codeql %} tools for scanning.
      - name: Initialize {% data variables.product.prodname_codeql %}
        uses: github/codeql-action/init@v1

      ...
```

Para obtener más información sobre la edición del archivo de flujo de trabajo de {% data variables.product.prodname_codeql %}, consulta la sección "[Configurar {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/configuring-code-scanning#editing-a-code-scanning-workflow)".
