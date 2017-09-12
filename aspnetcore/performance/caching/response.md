---
title: "Las respuestas en caché"
author: rick-anderson
description: "Explica cómo utilizar el almacenamiento en caché para reducir el ancho de banda y mejorar el rendimiento de respuesta."
keywords: "Núcleo de ASP.NET, almacenamiento en caché, encabezados HTTP de respuesta"
ms.author: riande
manager: wpickett
ms.date: 7/10/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 8b20ac70f257213ae3830749e729156ee5ab6447
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2017
---
# <a name="response-caching"></a><span data-ttu-id="8bf0e-104">Las respuestas en caché</span><span class="sxs-lookup"><span data-stu-id="8bf0e-104">Response Caching</span></span>

<span data-ttu-id="8bf0e-105">Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8bf0e-105">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

[<span data-ttu-id="8bf0e-106">Ver o descargar el código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="8bf0e-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)

## <a name="what-is-response-caching"></a><span data-ttu-id="8bf0e-107">¿Qué es el almacenamiento en caché de respuesta</span><span class="sxs-lookup"><span data-stu-id="8bf0e-107">What is Response Caching</span></span>

<span data-ttu-id="8bf0e-108">*Las respuestas en caché* aumenta encabezados relacionados con la memoria caché las respuestas.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-108">*Response caching* adds cache-related headers to responses.</span></span> <span data-ttu-id="8bf0e-109">Estos encabezados especifican cómo desea que cliente, el proxy y middleware para la memoria caché las respuestas.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-109">These headers specify how you want client, proxy and middleware to cache responses.</span></span> <span data-ttu-id="8bf0e-110">Las respuestas en caché pueden reducir el número de solicitudes de que un cliente o proxy realiza al servidor web.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-110">Response caching can reduce the number of requests a client or proxy makes to the web server.</span></span> <span data-ttu-id="8bf0e-111">Las respuestas en caché también pueden reducir la cantidad de trabajo realiza el servidor web para generar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-111">Response caching can also reduce the amount of work the web server performs to generate the response.</span></span> 

<span data-ttu-id="8bf0e-112">El encabezado HTTP principal que se usa para almacenar en caché es `Cache-Control`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-112">The primary HTTP header used for caching is `Cache-Control`.</span></span> <span data-ttu-id="8bf0e-113">Consulte la [caché de HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-113">See the [HTTP 1.1 Caching](https://tools.ietf.org/html/rfc7234#section-5.2) for more information.</span></span> <span data-ttu-id="8bf0e-114">Directivas de caché comunes:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-114">Common cache directives:</span></span>

* [<span data-ttu-id="8bf0e-115">public</span><span class="sxs-lookup"><span data-stu-id="8bf0e-115">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)
* [<span data-ttu-id="8bf0e-116">private</span><span class="sxs-lookup"><span data-stu-id="8bf0e-116">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)
* [<span data-ttu-id="8bf0e-117">sin caché</span><span class="sxs-lookup"><span data-stu-id="8bf0e-117">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)
* [<span data-ttu-id="8bf0e-118">Pragma</span><span class="sxs-lookup"><span data-stu-id="8bf0e-118">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)
* [<span data-ttu-id="8bf0e-119">Variar</span><span class="sxs-lookup"><span data-stu-id="8bf0e-119">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)

<span data-ttu-id="8bf0e-120">El servidor web puede almacenar en caché las respuestas mediante la adición de la respuesta de almacenamiento en caché de middleware.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-120">The web server can cache responses by adding the response caching middleware.</span></span> <span data-ttu-id="8bf0e-121">Vea [respuesta almacenamiento en caché middleware](middleware.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-121">See [Response caching middleware](middleware.md) for more information.</span></span>

## <a name="distributed-cache-tag-helper"></a><span data-ttu-id="8bf0e-122">Aplicación auxiliar de etiqueta de caché distribuida</span><span class="sxs-lookup"><span data-stu-id="8bf0e-122">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="8bf0e-123">El [auxiliar de etiqueta de caché distribuida](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) permite caching distribuido.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-123">The [Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/DistributedCacheTagHelper) enables distributed caching.</span></span>


## <a name="responsecache-attribute"></a><span data-ttu-id="8bf0e-124">Atributo ResponseCache</span><span class="sxs-lookup"><span data-stu-id="8bf0e-124">ResponseCache Attribute</span></span>

<span data-ttu-id="8bf0e-125">El `ResponseCacheAttribute` especifica los parámetros necesarios para establecer encabezados apropiados en las respuestas en caché.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-125">The `ResponseCacheAttribute` specifies the parameters necessary for setting appropriate headers in response caching.</span></span> <span data-ttu-id="8bf0e-126">Vea [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute) para obtener una descripción de los parámetros.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-126">See [ResponseCacheAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)  for a description of the parameters.</span></span>

>[!WARNING]
> <span data-ttu-id="8bf0e-127">Deshabilitar el almacenamiento en caché para el contenido que contiene información para clientes autenticados.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-127">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="8bf0e-128">Almacenamiento en caché solo debería habilitarse para contenido que no cambia en función de la identidad de un usuario, o si un usuario ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-128">Caching should only be enabled for content that does not change based on a user's identity, or whether a user is logged in.</span></span>

<span data-ttu-id="8bf0e-129">`VaryByQueryKeys string[]`(requiere ASP.NET Core 1.1.0 y versiones posteriores): cuando se establece, la respuesta de almacenamiento en caché middleware variará la respuesta almacenada en los valores de la lista de claves de consulta determinada.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-129">`VaryByQueryKeys string[]` (requires ASP.NET Core 1.1.0 and higher): When set, the response caching middleware will vary the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="8bf0e-130">La respuesta middleware el almacenamiento en caché debe estar habilitada para establecer el `VaryByQueryKeys` propiedad, en caso contrario, una excepción en tiempo de ejecución se producirá.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-130">The response caching middleware must be enabled to set the `VaryByQueryKeys` property, otherwise a runtime exception will be thrown.</span></span> <span data-ttu-id="8bf0e-131">No hay ningún encabezado HTTP correspondiente para el `VaryByQueryKeys` propiedad.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-131">There is no corresponding HTTP header for the `VaryByQueryKeys` property.</span></span> <span data-ttu-id="8bf0e-132">Esta propiedad es una característica HTTP controlada la respuesta middleware el almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-132">This property is an HTTP feature handled by the response caching middleware.</span></span> <span data-ttu-id="8bf0e-133">Para que el middleware atender una respuesta almacenada en caché, la cadena de consulta y el valor de cadena de consulta deben coincidir con una solicitud anterior.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-133">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="8bf0e-134">Por ejemplo, considere la siguiente secuencia:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-134">For example, consider the following sequence:</span></span>

| <span data-ttu-id="8bf0e-135">Solicitud</span><span class="sxs-lookup"><span data-stu-id="8bf0e-135">Request</span></span>          | <span data-ttu-id="8bf0e-136">Resultado</span><span class="sxs-lookup"><span data-stu-id="8bf0e-136">Result</span></span> |
| ----------------- | ------------ | 
| `http://example.com?key1=value1` | <span data-ttu-id="8bf0e-137">devuelta por el servidor</span><span class="sxs-lookup"><span data-stu-id="8bf0e-137">returned from server</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="8bf0e-138">procedentes de middleware</span><span class="sxs-lookup"><span data-stu-id="8bf0e-138">returned from middleware</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="8bf0e-139">devuelta por el servidor</span><span class="sxs-lookup"><span data-stu-id="8bf0e-139">returned from server</span></span> |

<span data-ttu-id="8bf0e-140">La primera solicitud es devuelto por el servidor y en memoria caché de middleware.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-140">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="8bf0e-141">Dado que la cadena de consulta coincide con la solicitud anterior, se devuelve la segunda solicitud middleware.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-141">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="8bf0e-142">La tercera solicitud no está en la caché de middleware porque el valor de cadena de consulta no coincide con una solicitud anterior.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-142">The third request is not in the middleware cache because the query string value doesn't match a previous request.</span></span> 

<span data-ttu-id="8bf0e-143">El `ResponseCacheAttribute` se utiliza para crear y configurar (a través de `IFilterFactory`) un `ResponseCacheFilter`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-143">The `ResponseCacheAttribute` is used to configure and create (via `IFilterFactory`) a `ResponseCacheFilter`.</span></span> <span data-ttu-id="8bf0e-144">El `ResponseCacheFilter` realiza el trabajo de actualización de los encabezados HTTP adecuados y características de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-144">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="8bf0e-145">El filtro:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-145">The filter:</span></span>

* <span data-ttu-id="8bf0e-146">Quita los encabezados existentes para `Vary`, `Cache-Control`, y `Pragma`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-146">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span> 
* <span data-ttu-id="8bf0e-147">Escribe los encabezados adecuados en función de las propiedades establecidas en el `ResponseCacheAttribute`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-147">Writes out the appropriate headers based on the properties set in the `ResponseCacheAttribute`.</span></span> 
* <span data-ttu-id="8bf0e-148">Actualiza la respuesta si el almacenamiento en caché característica HTTP `VaryByQueryKeys` se establece.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-148">Updates the response caching HTTP feature if `VaryByQueryKeys` is set.</span></span>

### <a name="vary"></a><span data-ttu-id="8bf0e-149">Variar</span><span class="sxs-lookup"><span data-stu-id="8bf0e-149">Vary</span></span>

<span data-ttu-id="8bf0e-150">Este encabezado solo cuando se escribe el `VaryByHeader` se establece la propiedad.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-150">This header is only written when the `VaryByHeader` property is set.</span></span> <span data-ttu-id="8bf0e-151">Se establece en el `Vary` valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-151">It is set to the `Vary` property's value.</span></span> <span data-ttu-id="8bf0e-152">El siguiente ejemplo se utiliza la `VaryByHeader` propiedad.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-152">The following sample uses the `VaryByHeader` property.</span></span>

<span data-ttu-id="8bf0e-153">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="8bf0e-153">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]</span></span>

<span data-ttu-id="8bf0e-154">Puede ver los encabezados de respuesta con las herramientas de red de los exploradores.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-154">You can view the response headers with your browsers network tools.</span></span> <span data-ttu-id="8bf0e-155">La siguiente imagen muestra la F12 de borde de salida en el **red** ficha cuando el `About2` se actualiza el método de acción.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-155">The following image shows the Edge F12 output on the **Network** tab when the `About2` action method is refreshed.</span></span> 

![Salida de F12 de arista en la ** red ** pestaña cuando se llama al método de acción 'About2'](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a><span data-ttu-id="8bf0e-157">NoStore y Location.None</span><span class="sxs-lookup"><span data-stu-id="8bf0e-157">NoStore and Location.None</span></span>

<span data-ttu-id="8bf0e-158">`NoStore`invalida la mayoría de las demás propiedades.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-158">`NoStore` overrides most of the other properties.</span></span> <span data-ttu-id="8bf0e-159">Cuando esta propiedad se establece en `true`, el `Cache-Control` encabezado se establecerá en "no-store".</span><span class="sxs-lookup"><span data-stu-id="8bf0e-159">When this property is set to `true`, the `Cache-Control` header will be set to "no-store".</span></span> <span data-ttu-id="8bf0e-160">Si `Location` está establecido en `None`:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-160">If `Location` is set to `None`:</span></span>

* <span data-ttu-id="8bf0e-161">El valor de `Cache-Control` está establecido en `"no-store, no-cache"`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-161">`Cache-Control` is set to `"no-store, no-cache"`.</span></span> 
* <span data-ttu-id="8bf0e-162">El valor de `Pragma` está establecido en `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-162">`Pragma` is set to `no-cache`.</span></span> 

<span data-ttu-id="8bf0e-163">Si `NoStore` es `false` y `Location` es `None`, `Cache-Control` y `Pragma` se establecerá en `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-163">If `NoStore` is `false` and `Location` is `None`,  `Cache-Control` and `Pragma` will be set to `no-cache`.</span></span>

<span data-ttu-id="8bf0e-164">Normalmente establece `NoStore` a `true` en las páginas de error.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-164">You typically set `NoStore` to `true` on error pages.</span></span> <span data-ttu-id="8bf0e-165">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-165">For example:</span></span>

<span data-ttu-id="8bf0e-166">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="8bf0e-166">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]</span></span>

<span data-ttu-id="8bf0e-167">Esto dará como resultado de los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-167">This will result in the following headers:</span></span>

```javascript
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="8bf0e-168">Ubicación y la duración</span><span class="sxs-lookup"><span data-stu-id="8bf0e-168">Location and Duration</span></span>

<span data-ttu-id="8bf0e-169">Para habilitar el almacenamiento en caché, `Duration` debe establecerse en un valor positivo y `Location` debe ser `Any` (valor predeterminado) o `Client`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-169">To enable caching, `Duration` must be set to a positive value and `Location` must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="8bf0e-170">En este caso, el `Cache-Control` encabezado se establecerá en el valor de ubicación seguido por "max-age" de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-170">In this case, the `Cache-Control` header will be set to the location value followed by the "max-age" of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="8bf0e-171">`Location`de opciones de `Any` y `Client` traducir `Cache-Control` valores de encabezado `public` y `private`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-171">`Location`'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="8bf0e-172">Como se indicó anteriormente, establecer `Location` a `None` establecerá ambos `Cache-Control` y `Pragma` encabezados para `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-172">As noted previously, setting `Location` to `None` will set both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="8bf0e-173">A continuación se muestra un ejemplo que muestra los encabezados genera estableciendo `Duration` y deja el valor predeterminado `Location` valor.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-173">Below is an example showing the headers produced by setting `Duration` and leaving the default `Location` value.</span></span>

<span data-ttu-id="8bf0e-174">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]</span><span class="sxs-lookup"><span data-stu-id="8bf0e-174">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]</span></span>

<span data-ttu-id="8bf0e-175">Genera los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-175">Produces the following headers:</span></span>

```javascript
Cache-Control: public,max-age=60
   ```

### <a name="cache-profiles"></a><span data-ttu-id="8bf0e-176">Perfiles de memoria caché</span><span class="sxs-lookup"><span data-stu-id="8bf0e-176">Cache Profiles</span></span>

<span data-ttu-id="8bf0e-177">En lugar de duplicar `ResponseCache` configuración en muchos atributos de acción de controlador, perfiles de memoria caché se puede configurar como opciones al configurar MVC en la `ConfigureServices` método `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-177">Instead of duplicating `ResponseCache` settings on many controller action attributes, cache profiles can be configured as options when setting up MVC in the `ConfigureServices` method in `Startup`.</span></span> <span data-ttu-id="8bf0e-178">Valores que se encuentran en un perfil de caché que se hace referencia se usará como los valores predeterminados por el `ResponseCache` de atributo y serán reemplazadas por las propiedades especificadas en el atributo.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-178">Values found in a referenced cache profile will be used as the defaults by the `ResponseCache` attribute, and will be overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="8bf0e-179">Cómo configurar un perfil de caché:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-179">Setting up a cache profile:</span></span>

<span data-ttu-id="8bf0e-180">[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="8bf0e-180">[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)]</span></span> 

<span data-ttu-id="8bf0e-181">Hacer referencia a un perfil de caché:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-181">Referencing a cache profile:</span></span>

<span data-ttu-id="8bf0e-182">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]</span><span class="sxs-lookup"><span data-stu-id="8bf0e-182">[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]</span></span>

<span data-ttu-id="8bf0e-183">El `ResponseCache` atributo se puede aplicar tanto para acciones (métodos), así como para controladores (clases).</span><span class="sxs-lookup"><span data-stu-id="8bf0e-183">The `ResponseCache` attribute can be applied both to actions (methods) as well as controllers (classes).</span></span> <span data-ttu-id="8bf0e-184">Atributos de nivel de método invalidará la configuración especificada en los atributos de nivel de clase.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-184">Method-level attributes will override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="8bf0e-185">En el ejemplo anterior, un atributo de nivel de clase especifica una duración de 30 segundos, mientras que un atributos de nivel de método hace referencia a un perfil de caché con una duración establecida en 60 segundos.</span><span class="sxs-lookup"><span data-stu-id="8bf0e-185">In the above example, a class-level attribute specifies a duration of 30 seconds while a method-level attributes references a cache profile with a duration set to 60 seconds.</span></span>

<span data-ttu-id="8bf0e-186">El encabezado resultante:</span><span class="sxs-lookup"><span data-stu-id="8bf0e-186">The resulting header:</span></span>

```
Cache-Control: public,max-age=60
   ```

  ### <a name="additional-resources"></a><span data-ttu-id="8bf0e-187">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8bf0e-187">Additional Resources</span></span>

* [<span data-ttu-id="8bf0e-188">Almacenamiento en caché de HTTP de la especificación</span><span class="sxs-lookup"><span data-stu-id="8bf0e-188">Caching in HTTP from the specification</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="8bf0e-189">Control de caché</span><span class="sxs-lookup"><span data-stu-id="8bf0e-189">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)