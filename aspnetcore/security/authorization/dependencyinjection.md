---
title: "Inserción de dependencias en los controladores de requisito"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5fb6625c-173a-4feb-8380-73c9844dc23c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 308951a45ee6576f096e1cdc792208b89e476e61
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="dependency-injection-in-requirement-handlers"></a>Inserción de dependencias en los controladores de requisito

<a name="security-authorization-di"></a>

[Controladores de autorización deben estar registrados](policies.md#security-authorization-policies-based-handler-registration) en la colección de servicio durante la configuración (mediante [inyección de dependencia](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).

Suponga que tiene un repositorio de reglas que desea evaluar dentro de un controlador de autorización y ese repositorio se ha registrado en la colección de servicio.  La autorización se resuelva e insertar en el constructor.

Por ejemplo, si desea utilizar ASP. NET del registro de infraestructura que desea insertar `ILoggerFactory` en el controlador. Un controlador de este tipo podría ser similar:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Debería registrar el controlador con `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
   ```

Una instancia de la voluntad de controlador se crea cuando se inicia la aplicación y se DI inyectar registrado `ILoggerFactory` en su constructor.

> [!NOTE]
> Los controladores que usan Entity Framework no deben registrarse como singleton.
