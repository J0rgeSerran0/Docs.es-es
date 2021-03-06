---
title: Otras API
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 72274a0da94a14bcd5af11d245ba626214fa2607
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="miscellaneous-apis"></a>Otras API

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguro para subprocesos para distintos llamadores.

## <a name="isecret"></a>ISecret

La interfaz ISecret representa un valor secreto, como material de clave de cifrado. Contiene la superficie de API siguiente.

* Longitud: int

* Dispose(): void

* WriteSecretIntoBuffer (ArraySegment<byte> búfer): void

El método WriteSecretIntoBuffer rellena el búfer proporcionado con el valor sin formato del secreto. La razón de que esta API toma el búfer como un parámetro en lugar de devolver que un byte [] es directamente Esto da al llamador la oportunidad para anclar el objeto de búfer, limitar la exposición de secreto para el recolector de elementos no utilizados administrado.

El tipo de secreto es una implementación concreta de ISecret donde el valor secreto se almacena en memoria en el proceso. En plataformas de Windows, el valor secreto se cifra mediante [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
