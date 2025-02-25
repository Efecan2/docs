---
title: Acercad e las acciones personalizadas
intro: 'Las acciones son tareas individuales que puedes combinar para crear trabajos y personalizar tu flujo de trabajo. Puedes crear tus propias acciones, o utilizar y personalizar a quellas que comparte la comunidad de {% data variables.product.prodname_dotcom %}.'
redirect_from:
  - /articles/about-actions
  - /github/automating-your-workflow-with-github-actions/about-actions
  - /actions/automating-your-workflow-with-github-actions/about-actions
  - /actions/building-actions/about-actions
  - /actions/creating-actions/about-actions
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
type: overview
topics:
  - Action development
  - Fundamentals
---

{% data reusables.actions.enterprise-beta %}
{% data reusables.actions.enterprise-github-hosted-runners %}

## Acercad e las acciones personalizadas

Puedes crear acciones por medio de a escritura de un código personalizado que interactúe con tu repositorio de la manera que desees, incluida la integración con las API de {% data variables.product.prodname_dotcom %} y cualquier API de terceros disponible públicamente. Por ejemplo, una acción puede publicar módulos npm, enviar alertas por SMS cuando se crean propuestas urgentes o implementar un código listo para producción.

{% ifversion fpt or ghec %}
Puedes escribir tus propias acciones para usar en tu flujo de trabajo o compartir las acciones que crees con la comunidad {% data variables.product.prodname_dotcom %}. Para compartir las acciones que creaste con todos, tu repositorio debe ser público. {% if internal-actions %}Para compartir las acciones únicamente con tu empresa, tu repositorio debe ser interno.{% endif %}
{% endif %}

Las acciones pueden ejecutarse directamente en una máquina o en un contenedor Docker. Puedes definir las entradas, salidas y variables de entorno de una acción.

## Tipos de acciones

Puedes crear acciones de contenedor Docker y JavaScript. Las acciones requieren un archivo de metadatos para definir las entradas, salidas y puntos de entrada para tu acción. El nombre del archivo de metadatos debe ser `action.yml` o `action.yaml`. Para obtener más información, consulta "[Sintaxis de metadatos para {% data variables.product.prodname_actions %}](/articles/metadata-syntax-for-github-actions)"

| Tipo                | Sistema operativo     |
| ------------------- | --------------------- |
| Contenedor Docker   | Linux                 |
| JavaScript          | Linux, macOS, Windows |
| Acciones compuestas | Linux, macOS, Windows |

### Acciones del contenedor Docker

Los contenedores Docker empaquetan el entorno con el código de las {% data variables.product.prodname_actions %}. Esto crea una unidad de trabajo más consistente y confiable, ya que el consumidor de la acción no necesita preocuparse por las herramientas o las dependencias.

Un contenedor Docker te permite usar versiones específicas de un sistema operativo, dependencias, herramientas y código. Para las acciones que se deben ejecutar en una configuración de entorno específica, Docker es una opción ideal porque puedes personalizar el sistema operativo y las herramientas. Debido a la latencia para crear y recuperar el contenedor, las acciones del contenedor Docker son más lentas que las acciones de JavaScript.

Las acciones de contenedor de Docker solo pueden ejecutarse en ejecutores con un sistema operativo Linux. {% data reusables.actions.self-hosted-runner-reqs-docker %}

### Acciones de JavaScript

Las acciones de JavaScript pueden ejecutarse directamente en una máquina del ejecutor y separar el código de acción del entorno utilizado para ejecutar el código. El uso de una acción de JavaScript simplifica el código de acción y se ejecuta más rápido que una acción de contenedor Docker.

{% data reusables.actions.pure-javascript %}

Si estás desarrollando un proyecto Node.js, el conjunto de herramientas de las {% data variables.product.prodname_actions %} te ofrece paquetes que puedes usar en tu proyecto para acelerar el desarrollo. Para obtener más información, consulta el repositorio [actions/toolkit](https://github.com/actions/toolkit).

### Acciones compuestas

Una acción _compuesta_ te permite combinar pasos de flujo de trabajo múltiples dentro de una acción. Por ejemplo, puedes utilizar esta característica para agrupar comandos de ejecución múltiples en una acción y luego tener un flujo de trabajo que ejecute estos comandos agrupados como un paso simple utilizando dicha acción. Para ver un ejemplo, revisa la sección "[Crear una acción compuesta](/actions/creating-actions/creating-a-composite-action)".

## Elegir una ubicación para tu acción

Si estás desarrollando una acción para que otras personas la utilicen, te recomendamos mantener la acción en su propio repositorio en lugar de agruparla con otro código de aplicación. Esto te permite versionar, rastrear y lanzar la acción como cualquier otro software.

{% ifversion fpt or ghec %}
Con el almacenamiento de una acción en su propio repositorio es más fácil para la comunidad {% data variables.product.prodname_dotcom %} descubrir la acción, reduce el alcance de la base de código para que los desarrolladores solucionen problemas y extiendan la acción, y desacopla el control de versiones de otro código de aplicación.
{% endif %}

{% data reusables.actions.internal-actions-summary %}

{% ifversion fpt or ghec %}Si estás creando una acción que no piensas poner disponible para otros, puedes {% else %} Puedes {% endif %} almacenar los archivos de dicha acción en cualquier ubicación de tu repositorio. Si tienes la intención de combinar la acción, el flujo de trabajo y el código de aplicación en un único repositorio, es recomendable que almacenes las acciones en el directorio `.github`. Por ejemplo, `.github/actions/action-a` y `.github/actions/action-b`.

## Compatibilidad con {% data variables.product.prodname_ghe_server %}

Para garantizar de que tu acción es compatible con {% data variables.product.prodname_ghe_server %}, debes asegurarte de que no utilices ninguna referencia escrita a mano para las URL de la API de {% ifversion fpt or ghec %}{% data variables.product.prodname_dotcom %}{% else %}{% data variables.product.product_name %}{% endif %}. En vez de esto, deberías utilizar variables de ambiente para referirte a la API de {% ifversion fpt or ghec %}{% data variables.product.prodname_dotcom %}{% else %}{% data variables.product.product_name %}{% endif %}:

- Crear y validar un lanzamiento en una rama de lanzamiento (tal como `release/v1`) antes de crear la etiqueta de lanzamiento (por ejemplo, `v1.0.2`).
- Para el caso de GraphQL, utiliza la variable de ambiente `GITHUB_GRAPHQL_URL`.

Para obtener más información, consulta la sección "[Variables de ambiente predeterminadas](/actions/configuring-and-managing-workflows/using-environment-variables#default-environment-variables)."

## Utilizar la administración de lanzamientos para las acciones

Esta sección explica cómo puedes utilizar la administración de lanzamientos para distribuir actualizaciones a tus acciones de forma predecible.

### Buenas prácticas para la administración de lanzamientos

Si estás desarrollando una acción para que la utilicen otras personas, te recomendamos utilizar la administración de lanzamientos para controlar cómo distribuyes las actualizaciones. Los usuarios pueden esperar que una versión mayor de una acción incluya correcciones críticas y parches de seguridad necesarios y que se mantenga compatible con los flujos de trabajo existentes. Deberías considerar lanzar una versión mayor cada que tus cambios afecten la compatibilidad.

Bajo este acercamiento de administración de lanzamientos, los usuarios no deberían referenciar una rama predeterminada de una acción, ya que es probable que contenga el código más reciente y, en consecuencia, podría ser inestable. En vez de esto, puedes recomendar a tus usuarios que especifiquen una versión mayor cuando utilicen tu acción, y únicamente dirigirlos a una versión más específica si encuentran algún problema.

Para utilizar una versión específica de la acción, los usuarios pueden configurar su flujo de trabajo de {% data variables.product.prodname_actions %} para apuntar a una etiqueta, el SHA de una confirmación o a una rama denominada para un lanzamiento.

### Utilizar etiquetas para la administración de lanzamientos

Te recomendamos utilizar etiquetas para la administración de lanzamientos de acciones. Al utilizar este acercamiento, tus usuarios pueden distinguir claramente entre las versiones mayores y menores:

- Crear y validar un lanzamiento en una rama de lanzamiento (tal como `release/v1`) antes de crear la etiqueta de lanzamiento (por ejemplo, `v1.0.2`).
- Crear un lanzamiento utilizando un versionamiento semántico. Para obtener más información, consulta "[Creating releases](/articles/creating-a-label/) (Crear lanzamientos)".
- Mover la etiqueta de versión mayor (tal como `v1`, `v2`) para apuntar a la referencia de Git en el lanzamiento actual. Para obtener más información, consulta [Conceptos básicos de Git: etiquetas](https://git-scm.com/book/en/v2/Git-Basics-Tagging)".
- Introducir una etiqueta de versión mayor (`v2`) para los cambios que modificarán sustancialmente los flujos de trabajo existentes. Por ejemplo, un cambio importante será cambiar las entradas de una acción.
- Las versiones mayores pueden lanzarse inicialmente con una etiqueta de `beta` para indicar su estado, por ejemplo, `v2-beta`. La etiqueta `-beta` puede eliminarse entonces cuando esté listo.

Este ejemplo ilustra como un usuario puede referenciar una etiqueta de un lanzamiento mayor:

```yaml
steps:
    - uses: actions/javascript-action@v1
```

Este ejemplo ilustra como un usuario puede referenciar una etiqueta de lanzamiento de un parche específico:

```yaml
steps:
    - uses: actions/javascript-action@v1.0.1
```

### Utilizar ramas para la administración de lanzamientos

Si prefieres utilizar nombres de rama para la administración de lanzamientos, este ejemplo demuestra como referenciar una rama nombrada:

```yaml
steps:
    - uses: actions/javascript-action@v1-beta
```

### Utilizar el SHA de las confirmaciones para la administración de lanzamientos

Cada confirmación de Git recibe un valor calculado de SHA, el cual es único e inmutable. Los usuarios de tus acciones podrían preferir obtener un valor de SHA para la confirmación, ya que este acercamiento puede ser más confiable que especificar una etiqueta, la cual podría borrarse o moverse. Sin embargo, esto significa que los usuarios no recibirán ls actualizaciones posteriores que se hagan a la acción. You must use a commit's full SHA value, and not an abbreviated value.

```yaml
steps:
    - uses: actions/javascript-action@172239021f7ba04fe7327647b213799853a9eb89
```

## Crear un archivo README para tu acción

Si tienes la intención de compartir públicamente tu acción, te recomendamos crear un archivo README para ayudar a las personas a que aprendan a usar tu acción. Puedes incluir esta información en tu `README.md`:

- Una descripción detallada de lo que hace la acción.
- Argumentos necesarios de entrada y salida.
- Argumentos opcionales de entrada y salida.
- Secretos que utiliza la acción.
- Variables de entorno que utiliza la acción.
- Un ejemplo de cómo usar tu acción en un flujo de trabajo.

## Comparar {% data variables.product.prodname_actions %} para {% data variables.product.prodname_github_apps %}

{% data variables.product.prodname_marketplace %} ofrece herramientas para mejorar tu flujo de trabajo. Comprender las diferencias y los beneficios de cada herramienta te permitirá seleccionar la mejor herramienta para tu trabajo. Para obtener más información acerca de la creación de apps, consulta "[Acerca de las apps](/apps/about-apps/)".

### Fortalezas de las acciones y las aplicaciones de GitHub

Si bien tanto las {% data variables.product.prodname_actions %} como las {% data variables.product.prodname_github_apps %} proporcionan formas de crear herramientas de flujo de trabajo y automatización, pueden tener fortalezas que las hagan útiles en formas diferentes.

{% data variables.product.prodname_github_apps %}:
* Se ejecutan de manera persistente y pueden reaccionar rápidamente a los eventos.
* Funcionan bien cuando se necesitan datos de manera persistente.
* Funcionan mejor con las solicitudes de API que no consumen mucho tiempo.
* Se ejecutan en un servidor o infraestructura de computación que proporciones.

{% data variables.product.prodname_actions %}:
* Brindan automatización que puede realizar una integración continua y una implementación continua.
* Pueden ejecutarse directamente en máquinas de ejecutor o en contenedores Docker.
* Pueden incluir acceso a un clon de tu repositorio, lo que permite que las herramientas de implementación y publicación, los formateadores de código y las herramientas de la línea de comando accedan a tu código.
* No necesitan que implementas un código o que sirvas una aplicación.
* Tienen una interfaz simple para crear y usar secretos, que permite que las acciones interactúen con servicios de terceros sin la necesidad de almacenar las credenciales de la persona que utiliza la acción.

## Leer más

- "[Herramientas de desarrollo para {% data variables.product.prodname_actions %}](/articles/development-tools-for-github-actions)"
