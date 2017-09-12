---
title: Formato de almacenamiento de claves
author: tdykstra
description: 
keywords: "Núcleo de ASP.NET,"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e8996478-f7bf-4b58-bab4-7fdb5d8556c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: e761eaa406a9691e3fa36881d42c1a0c1bd8a206
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/11/2017
---
# <a name="key-storage-format"></a><span data-ttu-id="fcd53-103">Formato de almacenamiento de claves</span><span class="sxs-lookup"><span data-stu-id="fcd53-103">Key Storage Format</span></span>

<a name=data-protection-implementation-key-storage-format></a>

<span data-ttu-id="fcd53-104">Objetos se almacenan en reposo en la representación XML.</span><span class="sxs-lookup"><span data-stu-id="fcd53-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="fcd53-105">El directorio predeterminado para el almacenamiento de claves es % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span><span class="sxs-lookup"><span data-stu-id="fcd53-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="fcd53-106">El \<clave > elemento</span><span class="sxs-lookup"><span data-stu-id="fcd53-106">The \<key> element</span></span>

<span data-ttu-id="fcd53-107">Las claves existen como objetos de nivel superior en el repositorio de clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="fcd53-108">Por convención, las claves tienen el nombre de archivo **clave-{guid} .xml**, donde {guid} es el identificador de la clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="fcd53-109">Cada archivo de este tipo contiene una clave única.</span><span class="sxs-lookup"><span data-stu-id="fcd53-109">Each such file contains a single key.</span></span> <span data-ttu-id="fcd53-110">El formato del archivo es como sigue.</span><span class="sxs-lookup"><span data-stu-id="fcd53-110">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="fcd53-111">El \<clave > elemento contiene los siguientes atributos y elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="fcd53-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="fcd53-112">El identificador de clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-112">The key id.</span></span> <span data-ttu-id="fcd53-113">Este valor se trata como autorizados; el nombre de archivo es simplemente una nicety más legible.</span><span class="sxs-lookup"><span data-stu-id="fcd53-113">This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="fcd53-114">La versión de la \<clave > elemento, que actualmente se fija en 1.</span><span class="sxs-lookup"><span data-stu-id="fcd53-114">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="fcd53-115">Fecha de creación, activación y expiración de la clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-115">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="fcd53-116">Un \<descriptor > elemento, que contiene información sobre la implementación del cifrado autenticado dentro de esta clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-116">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="fcd53-117">En el ejemplo anterior, Id. de la clave es {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, que se creó o se activa en el 19 de marzo de 2015 y tiene una duración de 90 días.</span><span class="sxs-lookup"><span data-stu-id="fcd53-117">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="fcd53-118">(En ocasiones, la fecha de activación puede estar ligeramente antes de la fecha de creación como en este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="fcd53-118">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="fcd53-119">Esto es debido a un nit en la forma en que las API de trabajo y es inofensivas en la práctica).</span><span class="sxs-lookup"><span data-stu-id="fcd53-119">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="fcd53-120">El \<descriptor > elemento</span><span class="sxs-lookup"><span data-stu-id="fcd53-120">The \<descriptor> element</span></span>

<span data-ttu-id="fcd53-121">El exterior \<descriptor > elemento contiene un deserializerType de atributo, que es el nombre calificado con el ensamblado de un tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="fcd53-121">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="fcd53-122">Este tipo es responsable de leer interna \<descriptor > elemento y para analizar la información incluida en.</span><span class="sxs-lookup"><span data-stu-id="fcd53-122">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="fcd53-123">El formato en cuestión de la \<descriptor > elemento depende de la implementación de sistema de cifrado autenticado encapsulada por la clave y cada tipo de deserializador espera un formato ligeramente diferente para este.</span><span class="sxs-lookup"><span data-stu-id="fcd53-123">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="fcd53-124">En general, no obstante, este elemento contendrá información algorítmica (nombres, tipos, OID, o similar) y material de clave secreta.</span><span class="sxs-lookup"><span data-stu-id="fcd53-124">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="fcd53-125">En el ejemplo anterior, el descriptor especifica que ajuste esta clave de cifrado de AES-256-CBC + HMACSHA256 validación.</span><span class="sxs-lookup"><span data-stu-id="fcd53-125">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="fcd53-126">El \<encryptedSecret > elemento</span><span class="sxs-lookup"><span data-stu-id="fcd53-126">The \<encryptedSecret> element</span></span>

<span data-ttu-id="fcd53-127">Un <encryptedSecret> elemento que contiene la forma cifrada del material de clave secreta puede estar presente si [está habilitado el cifrado de secretos en reposo](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="fcd53-127">An <encryptedSecret> element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest).</span></span> <span data-ttu-id="fcd53-128">El atributo decryptorType será el nombre calificado con el ensamblado de un tipo que implementa IXmlDecryptor.</span><span class="sxs-lookup"><span data-stu-id="fcd53-128">The attribute decryptorType will be the assembly-qualified name of a type which implements IXmlDecryptor.</span></span> <span data-ttu-id="fcd53-129">Este tipo es responsable de leer interna <encryptedKey> elemento y el descifrado para recuperar el texto sin formato original.</span><span class="sxs-lookup"><span data-stu-id="fcd53-129">This type is responsible for reading the inner <encryptedKey> element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="fcd53-130">Al igual que con \<descriptor >, el formato en cuestión de la <encryptedSecret> elemento depende el mecanismo de cifrado en reposo en uso.</span><span class="sxs-lookup"><span data-stu-id="fcd53-130">As with \<descriptor>, the particular format of the <encryptedSecret> element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="fcd53-131">En el ejemplo anterior, la clave maestra se cifra mediante DPAPI de Windows por el comentario.</span><span class="sxs-lookup"><span data-stu-id="fcd53-131">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="fcd53-132">El \<revocación > elemento</span><span class="sxs-lookup"><span data-stu-id="fcd53-132">The \<revocation> element</span></span>

<span data-ttu-id="fcd53-133">Revocaciones existen como objetos de nivel superior en el repositorio de clave.</span><span class="sxs-lookup"><span data-stu-id="fcd53-133">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="fcd53-134">Por convención, las revocaciones tienen el nombre de archivo **revocación-{timestamp} .xml** (para revocar todas las claves antes de una fecha concreta) o **revocación-{guid} .xml** (para revocar una clave específica).</span><span class="sxs-lookup"><span data-stu-id="fcd53-134">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="fcd53-135">Cada archivo contiene un único \<revocación > elemento.</span><span class="sxs-lookup"><span data-stu-id="fcd53-135">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="fcd53-136">Para las revocaciones de las claves individuales, será el contenido del archivo a la siguiente.</span><span class="sxs-lookup"><span data-stu-id="fcd53-136">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="fcd53-137">En este caso, solo la clave especificada se ha revocado.</span><span class="sxs-lookup"><span data-stu-id="fcd53-137">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="fcd53-138">Si el identificador de clave es "*", sin embargo, como en el ejemplo siguiente, se revocan todas las claves cuya fecha de creación es anterior a la fecha de revocación especificada.</span><span class="sxs-lookup"><span data-stu-id="fcd53-138">If the key id is "*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="fcd53-139">El \<motivo > elemento nunca se lee por el sistema.</span><span class="sxs-lookup"><span data-stu-id="fcd53-139">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="fcd53-140">Es simplemente un lugar conveniente para almacenar un motivo de revocación legible.</span><span class="sxs-lookup"><span data-stu-id="fcd53-140">It is simply a convenient place to store a human-readable reason for revocation.</span></span>