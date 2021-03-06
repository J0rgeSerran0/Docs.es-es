---
title: "Ubicación de almacenamiento segura de secretos de aplicación durante el desarrollo de ASP.NET Core"
author: rick-anderson
description: "Muestra cómo almacenar secretos de forma segura durante el desarrollo"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 09/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 280819a6a0afb72311f0d50f7d3b83a942e9fcc3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>Ubicación de almacenamiento segura de secretos de aplicación durante el desarrollo de ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), y [Scott Addie](https://scottaddie.com) 

Este documento muestra cómo puede usar la herramienta Administrador de secreto en el desarrollo para mantener secretos fuera del código. El punto más importante es nunca debe almacenar las contraseñas u otros datos confidenciales en el código fuente y no utilice secretos de producción en modo de desarrollo y pruebas. En su lugar, puede usar el [configuración](../fundamentals/configuration.md) sistema para leer estos valores de variables de entorno o de valores almacenados mediante el Administrador de secreto de la herramienta. La herramienta Administrador de secreto ayuda a evitar que los datos confidenciales que se protegen en el control de código fuente. El [configuración](../fundamentals/configuration.md) sistema pueda leer los secretos almacenados con la herramienta Administrador de secreto se describe en este artículo.

La herramienta Administrador de secreto se usa solo en el desarrollo. Puede proteger los secretos de prueba y producción Azure con el [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) proveedor de configuración. Vea [proveedor de configuración de almacén de claves de Azure](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) para obtener más información.

## <a name="environment-variables"></a>Variables de entorno

Para evitar almacenar secretos de aplicación en el código o en archivos de configuración local, almacenar secretos en variables de entorno. Puede configurar la [configuración](../fundamentals/configuration.md) framework para leer los valores de variables de entorno mediante una llamada a `AddEnvironmentVariables`. A continuación, puede utilizar variables de entorno para reemplazar los valores de configuración para todos los orígenes de configuración especificada anteriormente.

Por ejemplo, si crea una nueva aplicación web de ASP.NET Core con cuentas de usuario individuales, agregará una cadena de conexión predeterminada para el *appSettings.JSON que se* archivo en el proyecto con la clave `DefaultConnection`. La cadena de conexión predeterminada está configurado para utilizar LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña. Al implementar la aplicación en un servidor de producción o de prueba, puede invalidar la `DefaultConnection` valor de clave con una configuración de variable de entorno que contiene la cadena de conexión (posiblemente con credenciales confidenciales) para una base de datos de producción o de prueba servidor.

>[!WARNING]
> Las variables de entorno se almacenan normalmente en texto sin formato y no se cifran. Si la máquina o el proceso se ve comprometido, las variables de entorno son accesibles por entidades de confianza. Saber qué medidas adicionales para evitar la divulgación de información confidencial del usuario todavía pueden ser necesarias.

## <a name="secret-manager"></a>Administrador de secreto

La herramienta Administrador de secreto almacena datos confidenciales en el trabajo de desarrollo fuera de su árbol de proyecto. La herramienta Administrador de secreto es una herramienta de proyecto que puede usarse para almacenar secretos de un [.NET Core](https://www.microsoft.com/net/core) proyecto durante el desarrollo. Con la herramienta Administrador de secreto, puede asociar los secretos de aplicación a un proyecto específico y compartirlos en varios proyectos.

>[!WARNING]
> La herramienta Administrador de secreto no cifra los secretos almacenados y no debe tratarse como un almacén de confianza. Es solo con fines de desarrollo. Las claves y los valores se almacenan en un archivo de configuración de JSON en el directorio del perfil de usuario.

## <a name="installing-the-secret-manager-tool"></a>Instalar la herramienta Administrador de secreto

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Haga clic en el proyecto en el Explorador de soluciones y seleccione **editar \<Nombre_proyecto\>.csproj** en el menú contextual. Agregue la línea resaltada en el *.csproj* y guardar archivos para restaurar el paquete NuGet asociado:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Haga clic de nuevo en el proyecto en el Explorador de soluciones y seleccione **administrar secretos del usuario** en el menú contextual. Este movimiento agrega un nuevo `UserSecretsId` nodo dentro de un `PropertyGroup` de la *.csproj* archivo, como se resalta en el ejemplo siguiente:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

Guardar modificados *.csproj* archivo también se abre un `secrets.json` archivo en el editor de texto. Reemplace el contenido de la `secrets.json` archivo con el código siguiente:

```json
{
    "MySecret": "ValueOfMySecret"
}
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Agregar `Microsoft.Extensions.SecretManager.Tools` a la *.csproj* de archivos y ejecutar `dotnet restore`. Puede usar los mismos pasos para instalar la herramienta de administrador de secreto mediante la línea de comandos.

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

Probar la herramienta Administrador de secreto ejecutando el siguiente comando:

```console
dotnet user-secrets -h
```

La herramienta Administrador de secreto mostrará uso, opciones y ayuda del comando.

> [!NOTE]
> Debe estar en el mismo directorio que el *.csproj* archivo para ejecutar las herramientas que se definen en el *.csproj* del archivo `DotNetCliToolReference` nodos.

La herramienta Administrador de secreto opera en valores de configuración específicos de proyecto que se almacenan en el perfil de usuario. Para utilizar secretos del usuario, debe especificar el proyecto una `UserSecretsId` valor en su *.csproj* archivo. El valor de `UserSecretsId` es arbitrario, pero es generalmente único para el proyecto. Los desarrolladores suelen generan un GUID para el `UserSecretsId`.

Agregar un `UserSecretsId` para el proyecto en el *.csproj* archivo:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

Utilice la herramienta Administrador de secreto para establecer un secreto. Por ejemplo, en una ventana de comandos desde el directorio del proyecto, escriba lo siguiente:

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

Puede ejecutar la herramienta Administrador de secreto desde otros directorios, pero debe utilizar el `--project` opción para pasar la ruta de acceso a la *.csproj* archivo:
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

También puede usar la herramienta Administrador de secreto para enumerar, quitar y borrar secretos de la aplicación.

-----

## <a name="accessing-user-secrets-via-configuration"></a>Obtener acceso a información confidencial del usuario mediante la configuración

Obtener acceso a los secretos de administrador de secreto a través del sistema de configuración. Agregar el `Microsoft.Extensions.Configuration.UserSecrets` empaquetar y ejecutar `dotnet restore`.

Agregar el origen de configuración de secretos de usuario para el `Startup` método:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

Puede tener acceso a los secretos del usuario a través de la API de configuración:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>Cómo funciona la herramienta Administrador de secreto

La herramienta Administrador de secreto abstrae los detalles de implementación, como dónde y cómo se almacenan los valores. Puede usar la herramienta sin conocer estos detalles de implementación. En la versión actual, los valores se almacenan en un [JSON](http://json.org/) archivo de configuración en el directorio del perfil de usuario:

* Windows:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

El valor de `userSecretsId` proviene del valor especificado en *.csproj* archivo.

No debe escribirse código que depende de la ubicación o el formato de los datos guardados con la herramienta Administrador de secreto, dado que podrían cambiar estos detalles de implementación. Por ejemplo, los valores secretos están *no* cifra hoy en día, pero también podría ser algún día.

## <a name="additional-resources"></a>Recursos adicionales

* [Configuración](../fundamentals/configuration.md)
