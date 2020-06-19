---
title: Миграция обработчиков и модулей HTTP в ASP.NET Core по промежуточного слоя
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 214e3fa86a1418f04a5e292cdc1b4baac8c75643
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074185"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="95ab0-102">Миграция обработчиков и модулей HTTP в ASP.NET Core по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="95ab0-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="95ab0-103">В этой статье показано, как перенести существующие [модули HTTP ASP.NET и обработчики с сервера System.](/iis/configuration/system.webserver/) в ASP.NET Core по [промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="95ab0-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="95ab0-104">Повторно посещаемые модули и обработчики</span><span class="sxs-lookup"><span data-stu-id="95ab0-104">Modules and handlers revisited</span></span>

<span data-ttu-id="95ab0-105">Прежде чем перейти к ASP.NET Core по промежуточного слоя, давайте сначала посмотрим, как работают HTTP-модули и обработчики:</span><span class="sxs-lookup"><span data-stu-id="95ab0-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Обработчик модулей](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="95ab0-107">**Обработчики:**</span><span class="sxs-lookup"><span data-stu-id="95ab0-107">**Handlers are:**</span></span>

* <span data-ttu-id="95ab0-108">Классы, реализующие [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="95ab0-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="95ab0-109">Используется для обработки запросов с заданным именем или расширением файла, например *. Report*</span><span class="sxs-lookup"><span data-stu-id="95ab0-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="95ab0-110">[Настроено](/iis/configuration/system.webserver/handlers/) в *Web.config*</span><span class="sxs-lookup"><span data-stu-id="95ab0-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="95ab0-111">**Модули:**</span><span class="sxs-lookup"><span data-stu-id="95ab0-111">**Modules are:**</span></span>

* <span data-ttu-id="95ab0-112">Классы, реализующие интерфейс [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="95ab0-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="95ab0-113">Вызывается для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="95ab0-113">Invoked for every request</span></span>

* <span data-ttu-id="95ab0-114">Возможность краткого замыкания (завершение дальнейшей обработки запроса)</span><span class="sxs-lookup"><span data-stu-id="95ab0-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="95ab0-115">Возможность добавления в HTTP-ответ или создания собственных</span><span class="sxs-lookup"><span data-stu-id="95ab0-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="95ab0-116">[Настроено](/iis/configuration/system.webserver/modules/) в *Web.config*</span><span class="sxs-lookup"><span data-stu-id="95ab0-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="95ab0-117">**Порядок, в котором модули обрабатывают входящие запросы, определяется следующим образом.**</span><span class="sxs-lookup"><span data-stu-id="95ab0-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="95ab0-118">[Жизненный цикл приложения](https://msdn.microsoft.com/library/ms227673.aspx)— это события серии, запускаемые ASP.NET: [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)и т. д. Каждый модуль может создать обработчик для одного или нескольких событий.</span><span class="sxs-lookup"><span data-stu-id="95ab0-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="95ab0-119">Для того же события порядок, в котором они настроены в *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="95ab0-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="95ab0-120">В дополнение к модулям можно добавлять обработчики для событий жизненного цикла в файл *Global.asax.CS* .</span><span class="sxs-lookup"><span data-stu-id="95ab0-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="95ab0-121">Эти обработчики запускаются после обработчиков в настроенных модулях.</span><span class="sxs-lookup"><span data-stu-id="95ab0-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="95ab0-122">От обработчиков и модулей до по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="95ab0-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="95ab0-123">**По промежуточного слоя проще, чем модули HTTP и обработчики:**</span><span class="sxs-lookup"><span data-stu-id="95ab0-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="95ab0-124">Модули, обработчики, *Global.asax.CS*, *Web.config* (за исключением конфигурации IIS) и жизненного цикла приложения исчезают</span><span class="sxs-lookup"><span data-stu-id="95ab0-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="95ab0-125">Роли обоих модулей и обработчиков были взяты по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="95ab0-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="95ab0-126">По промежуточного слоя настраивается с использованием кода, а не в *Web.config*</span><span class="sxs-lookup"><span data-stu-id="95ab0-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="95ab0-127">[Ветвление конвейера](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) позволяет отправлять запросы к определенному по промежуточного слоя, основываясь не только на URL-адресе, но также и в заголовках запросов, строках запросов и т. д.</span><span class="sxs-lookup"><span data-stu-id="95ab0-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="95ab0-128">[Ветвление конвейера](xref:fundamentals/middleware/index#use-run-and-map) позволяет отправлять запросы к определенному по промежуточного слоя, основываясь не только на URL-адресе, но также и в заголовках запросов, строках запросов и т. д.</span><span class="sxs-lookup"><span data-stu-id="95ab0-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="95ab0-129">**По промежуточного слоя очень похоже на модули:**</span><span class="sxs-lookup"><span data-stu-id="95ab0-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="95ab0-130">Вызывается в принципе для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="95ab0-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="95ab0-131">Возможность сокращения запроса путем [передачи запроса следующему по промежуточного слоя](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="95ab0-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="95ab0-132">Возможность создания собственного HTTP-ответа</span><span class="sxs-lookup"><span data-stu-id="95ab0-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="95ab0-133">**По промежуточного слоя и модули обрабатываются в другом порядке:**</span><span class="sxs-lookup"><span data-stu-id="95ab0-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="95ab0-134">Порядок по промежуточного слоя основан на порядке, в котором они вставляются в конвейер запросов, а порядок модулей в основном основан на событиях [жизненного цикла приложения](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="95ab0-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="95ab0-135">Порядок по промежуточного слоя для ответов является обратным по отношению к запросам, а порядок модулей одинаков для запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="95ab0-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="95ab0-136">См. раздел [создание конвейера по промежуточного слоя с помощью IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) .</span><span class="sxs-lookup"><span data-stu-id="95ab0-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![ПО промежуточного слоя](http-modules/_static/middleware.png)

<span data-ttu-id="95ab0-138">Обратите внимание на то, как на приведенном выше рисунке промежуточное по проверки подлинности сокращено по запросу.</span><span class="sxs-lookup"><span data-stu-id="95ab0-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="95ab0-139">Перенос кода модуля на промежуточное по</span><span class="sxs-lookup"><span data-stu-id="95ab0-139">Migrating module code to middleware</span></span>

<span data-ttu-id="95ab0-140">Существующий HTTP-модуль будет выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="95ab0-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="95ab0-141">Как показано на странице по [промежуточного слоя](xref:fundamentals/middleware/index) , ASP.NET Core по промежуточного слоя представляет собой класс, который предоставляет `Invoke` метод, принимающий `HttpContext` и возвращающий `Task` .</span><span class="sxs-lookup"><span data-stu-id="95ab0-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="95ab0-142">Новое по промежуточного слоя будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="95ab0-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="95ab0-143">Предыдущий шаблон по промежуточного слоя был взят из раздела о [создании по промежуточного слоя](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="95ab0-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="95ab0-144">Вспомогательный класс *мимиддлеварикстенсионс* упрощает настройку по промежуточного слоя в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="95ab0-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="95ab0-145">`UseMyMiddleware`Метод добавляет ваш класс по промежуточного слоя в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="95ab0-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="95ab0-146">Службы, необходимые по промежуточного слоя, вставляются в конструктор по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="95ab0-147">Ваш модуль может завершить запрос, например, если пользователь не является полномочным:</span><span class="sxs-lookup"><span data-stu-id="95ab0-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="95ab0-148">По промежуточного слоя обрабатывает это, не вызывая `Invoke` для следующего по промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="95ab0-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="95ab0-149">Помните, что это не полностью завершает запрос, поскольку предыдущие промежуточные по по-прежнему будут вызываться, когда ответ обратно проходит через конвейер.</span><span class="sxs-lookup"><span data-stu-id="95ab0-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="95ab0-150">При переносе функциональных возможностей модуля в новое по промежуточного слоя может оказаться, что код не компилируется, поскольку `HttpContext` класс сильно изменился в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab0-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="95ab0-151">[Позже](#migrating-to-the-new-httpcontext)вы увидите, как перейти на новый ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="95ab0-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="95ab0-152">Перенос вставки модуля в конвейер запросов</span><span class="sxs-lookup"><span data-stu-id="95ab0-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="95ab0-153">Модули HTTP обычно добавляются в конвейер запросов с помощью *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="95ab0-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="95ab0-154">Преобразуйте это, [добавив новое по промежуточного слоя](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в конвейер запросов в своем `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="95ab0-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="95ab0-155">Точное место в конвейере, где вы вставляете новое по промежуточного слоя, зависит от события, которое оно обрабатывает как модуль ( `BeginRequest` , и `EndRequest` т. д.) и его порядок в списке модулей в *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="95ab0-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="95ab0-156">Как было сказано выше, в ASP.NET Core нет жизненного цикла приложения, и порядок обработки ответов по промежуточного слоя отличается от порядка, используемого модулями.</span><span class="sxs-lookup"><span data-stu-id="95ab0-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="95ab0-157">Это может сделать решение по упорядочиванию более сложным.</span><span class="sxs-lookup"><span data-stu-id="95ab0-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="95ab0-158">Если заказ станет проблемой, модуль можно разделить на несколько компонентов по промежуточного слоя, которые могут быть упорядочены независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="95ab0-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="95ab0-159">Перенос кода обработчика в по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="95ab0-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="95ab0-160">Обработчик HTTP выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="95ab0-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="95ab0-161">В проекте ASP.NET Core это будет по промежуточного слоя, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="95ab0-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="95ab0-162">Это по промежуточного слоя очень похоже на по промежуточного слоя, соответствующее модулям.</span><span class="sxs-lookup"><span data-stu-id="95ab0-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="95ab0-163">Единственная реальная разница заключается в том, что здесь нет вызова `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="95ab0-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="95ab0-164">Это имеет смысл, так как обработчик находится в конце конвейера запросов, поэтому следующее промежуточное по не будет вызываться.</span><span class="sxs-lookup"><span data-stu-id="95ab0-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="95ab0-165">Миграция вставки обработчика в конвейер запросов</span><span class="sxs-lookup"><span data-stu-id="95ab0-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="95ab0-166">Настройка обработчика HTTP выполняется в *Web.config* и выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="95ab0-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="95ab0-167">Это можно преобразовать, добавив новое по промежуточного слоя обработчика в конвейер запросов в `Startup` классе, как и по промежуточного слоя, преобразованного из модулей.</span><span class="sxs-lookup"><span data-stu-id="95ab0-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="95ab0-168">Проблема этого подхода заключается в том, что она будет отсылать все запросы на новый по промежуточного слоя обработчика.</span><span class="sxs-lookup"><span data-stu-id="95ab0-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="95ab0-169">Однако запросы с заданным расширением должны достигать вашего по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="95ab0-170">Это позволит получить те же функциональные возможности, что и обработчик HTTP.</span><span class="sxs-lookup"><span data-stu-id="95ab0-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="95ab0-171">Одним из решений является ветвление конвейера для запросов с заданным расширением с помощью `MapWhen` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="95ab0-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="95ab0-172">Это делается в том же `Configure` методе, в котором вы добавляете другое по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="95ab0-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="95ab0-173">`MapWhen`принимает следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="95ab0-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="95ab0-174">Лямбда-выражение, которое принимает `HttpContext` и возвращает, `true` Если запрос должен проходить по ветке.</span><span class="sxs-lookup"><span data-stu-id="95ab0-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="95ab0-175">Это означает, что запросы ветвления можно выполнять не только на основе расширения, но и в заголовках запросов, параметрах строки запроса и т. д.</span><span class="sxs-lookup"><span data-stu-id="95ab0-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="95ab0-176">Лямбда-выражение, которое принимает `IApplicationBuilder` и добавляет все по промежуточного слоя для ветви.</span><span class="sxs-lookup"><span data-stu-id="95ab0-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="95ab0-177">Это означает, что можно добавить дополнительное промежуточное по в ветвь перед по промежуточного слоя обработчика.</span><span class="sxs-lookup"><span data-stu-id="95ab0-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="95ab0-178">По промежуточного слоя, добавленное в конвейер до вызова ветви для всех запросов; ветвь не будет оказывать влияния на них.</span><span class="sxs-lookup"><span data-stu-id="95ab0-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="95ab0-179">Загрузка параметров по промежуточного слоя с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="95ab0-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="95ab0-180">Некоторые модули и обработчики имеют параметры конфигурации, которые хранятся в *Web.config*. Однако в ASP.NET Core вместо *Web.config*используется новая модель конфигурации.</span><span class="sxs-lookup"><span data-stu-id="95ab0-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="95ab0-181">Новая [система конфигурации](xref:fundamentals/configuration/index) предоставляет следующие возможности для решения этой задачи:</span><span class="sxs-lookup"><span data-stu-id="95ab0-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="95ab0-182">Непосредственно внедрять параметры в по промежуточного слоя, как показано в [следующем разделе](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="95ab0-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="95ab0-183">Используйте [шаблон параметров](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="95ab0-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="95ab0-184">Создайте класс для хранения параметров по промежуточного слоя, например:</span><span class="sxs-lookup"><span data-stu-id="95ab0-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="95ab0-185">Сохранение значений параметров</span><span class="sxs-lookup"><span data-stu-id="95ab0-185">Store the option values</span></span>

   <span data-ttu-id="95ab0-186">Система конфигурации позволяет хранить значения параметров в любом месте.</span><span class="sxs-lookup"><span data-stu-id="95ab0-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="95ab0-187">Однако большинство сайтов использует *appsettings.jsв*, поэтому мы будем использовать такой подход:</span><span class="sxs-lookup"><span data-stu-id="95ab0-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="95ab0-188">*Мимиддлевареоптионссектион* здесь — это имя раздела.</span><span class="sxs-lookup"><span data-stu-id="95ab0-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="95ab0-189">Оно не должно совпадать с именем класса параметров.</span><span class="sxs-lookup"><span data-stu-id="95ab0-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="95ab0-190">Связывание значений параметров с классом Options</span><span class="sxs-lookup"><span data-stu-id="95ab0-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="95ab0-191">Шаблон параметров использует инфраструктуру внедрения зависимостей ASP.NET Core для связывания типа параметров (например, `MyMiddlewareOptions` ) с `MyMiddlewareOptions` объектом с фактическими параметрами.</span><span class="sxs-lookup"><span data-stu-id="95ab0-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="95ab0-192">Обновите свой `Startup` класс:</span><span class="sxs-lookup"><span data-stu-id="95ab0-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="95ab0-193">Если вы используете *appsettings.js*, добавьте его в построитель конфигураций в `Startup` конструкторе:</span><span class="sxs-lookup"><span data-stu-id="95ab0-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="95ab0-194">Настройте службу Options:</span><span class="sxs-lookup"><span data-stu-id="95ab0-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="95ab0-195">Свяжите свои параметры с классом Options:</span><span class="sxs-lookup"><span data-stu-id="95ab0-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="95ab0-196">Вставьте параметры в конструктор по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="95ab0-197">Это похоже на внедрение параметров в контроллер.</span><span class="sxs-lookup"><span data-stu-id="95ab0-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="95ab0-198">Метод расширения [усемиддлеваре](#http-modules-usemiddleware) , который добавляет по промежуточного слоя в, `IApplicationBuilder` отвечает за внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="95ab0-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="95ab0-199">Это не ограничивается `IOptions` объектами.</span><span class="sxs-lookup"><span data-stu-id="95ab0-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="95ab0-200">Любой другой объект, который требуется по промежуточного слоя, можно внедрить таким образом.</span><span class="sxs-lookup"><span data-stu-id="95ab0-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="95ab0-201">Загрузка параметров по промежуточного слоя через прямую вставку</span><span class="sxs-lookup"><span data-stu-id="95ab0-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="95ab0-202">Шаблон параметров имеет преимущество, что он создает слабую связь между значениями параметров и их потребителями.</span><span class="sxs-lookup"><span data-stu-id="95ab0-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="95ab0-203">После связывания класса Options с фактическими значениями параметров любой другой класс может получить доступ к параметрам через платформу внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="95ab0-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="95ab0-204">Нет необходимости передавать значения параметров.</span><span class="sxs-lookup"><span data-stu-id="95ab0-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="95ab0-205">Это разбивается, если вы хотите использовать одно по промежуточного слоя дважды с различными параметрами.</span><span class="sxs-lookup"><span data-stu-id="95ab0-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="95ab0-206">Например, по промежуточного слоя авторизации, используемого в разных ветвях, которые позволяют использовать разные роли.</span><span class="sxs-lookup"><span data-stu-id="95ab0-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="95ab0-207">Нельзя связать два различных объекта Options с одним классом параметров.</span><span class="sxs-lookup"><span data-stu-id="95ab0-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="95ab0-208">Решение заключается в получении объектов Options с фактическими значениями параметров в `Startup` классе и передачей их непосредственно в каждый экземпляр по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="95ab0-209">Добавьте второй ключ для *appsettings.jsна*</span><span class="sxs-lookup"><span data-stu-id="95ab0-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="95ab0-210">Чтобы добавить второй набор параметров к *appsettings.jsв* файле, используйте новый ключ для уникальной идентификации.</span><span class="sxs-lookup"><span data-stu-id="95ab0-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="95ab0-211">Извлеките значения параметров и передайте их в по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="95ab0-212">`Use...`Метод расширения (который добавляет свое по промежуточного слоя в конвейер) является логическим местом для передачи значений параметров:</span><span class="sxs-lookup"><span data-stu-id="95ab0-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="95ab0-213">Включение по промежуточного слоя для использования параметра options.</span><span class="sxs-lookup"><span data-stu-id="95ab0-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="95ab0-214">Предоставьте перегрузку `Use...` метода расширения (который принимает параметр options и передает его в `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="95ab0-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="95ab0-215">Когда `UseMiddleware` вызывается с параметрами, он передает параметры в конструктор по промежуточного слоя при создании экземпляра объекта по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="95ab0-216">Обратите внимание, что это создает оболочку для объекта Options в `OptionsWrapper` объекте.</span><span class="sxs-lookup"><span data-stu-id="95ab0-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="95ab0-217">В этом `IOptions` случае реализуется, как и ожидается конструктором по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="95ab0-218">Переход на новый элемент HttpContext</span><span class="sxs-lookup"><span data-stu-id="95ab0-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="95ab0-219">Ранее было показано, что `Invoke` метод в по промежуточного слоя принимает параметр типа `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="95ab0-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="95ab0-220">`HttpContext`значительно изменился в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab0-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="95ab0-221">В этом разделе показано, как преобразовать наиболее часто используемые свойства [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) в новый `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="95ab0-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="95ab0-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="95ab0-222">HttpContext</span></span>

<span data-ttu-id="95ab0-223">**Объекты HttpContext. Items** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="95ab0-224">**Уникальный идентификатор запроса (не является аналогом System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="95ab0-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="95ab0-225">Предоставляет уникальный идентификатор для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="95ab0-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="95ab0-226">Очень полезно включить в журналы.</span><span class="sxs-lookup"><span data-stu-id="95ab0-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="95ab0-227">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="95ab0-227">HttpContext.Request</span></span>

<span data-ttu-id="95ab0-228">**HttpContext. Request. HttpMethod** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="95ab0-229">**Запрос HttpContext. Request. QueryString** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="95ab0-230">**HttpContext. Request. URL** и **HttpContext. Request. равурл** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="95ab0-231">**HttpContext. Request. иссекуреконнектион** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="95ab0-232">**HttpContext. Request. усерхостаддресс** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="95ab0-233">**Запросы HttpContext. Request. cookie** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="95ab0-234">**Запрос HttpContext. Request. RequestContext. RouteData** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="95ab0-235">**Запросы HttpContext. Request. Headers** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="95ab0-236">**HttpContext. Request. UserAgent** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="95ab0-237">**HttpContext. Request. урлреферрер** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="95ab0-238">Преобразование **HttpContext. Request. ContentType** в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="95ab0-239">Преобразование **HttpContext. Request. Form** в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="95ab0-240">Чтение значений формы происходит только в том случае, если подтип содержимого имеет вид *x-www-Form-UrlEncoded* или *form-data*.</span><span class="sxs-lookup"><span data-stu-id="95ab0-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="95ab0-241">**HttpContext. Request. InputStream** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="95ab0-242">Этот код следует использовать только в по промежуточного слоя типа обработчика в конце конвейера.</span><span class="sxs-lookup"><span data-stu-id="95ab0-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="95ab0-243">Вы можете прочитать необработанный текст, как показано выше, для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="95ab0-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="95ab0-244">По промежуточного слоя, пытающегося прочитать текст после первого чтения, будет считать пустое тело.</span><span class="sxs-lookup"><span data-stu-id="95ab0-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="95ab0-245">Это не относится к чтению формы, как показано выше, так как это делается из буфера.</span><span class="sxs-lookup"><span data-stu-id="95ab0-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="95ab0-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="95ab0-246">HttpContext.Response</span></span>

<span data-ttu-id="95ab0-247">**HttpContext. Response. status** и **HttpContext. Response. StatusDescription** транслируются в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="95ab0-248">Преобразование **HttpContext. Response. ContentEncoding** и **HttpContext. Response. ContentType** в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="95ab0-249">**HttpContext. Response. ContentType** также преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="95ab0-250">**HttpContext. Response. Output** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="95ab0-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="95ab0-251">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="95ab0-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="95ab0-252">[Здесь](../fundamentals/request-features.md#middleware-and-request-features)обсуждается обслуживание файла.</span><span class="sxs-lookup"><span data-stu-id="95ab0-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="95ab0-253">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="95ab0-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="95ab0-254">Отправка заголовков ответа осложняется тем, что если вы задали их после того, как что-либо записало в текст ответа, они не будут отправлены.</span><span class="sxs-lookup"><span data-stu-id="95ab0-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="95ab0-255">Решением является установка метода обратного вызова, который будет вызываться прямо перед началом записи в ответ.</span><span class="sxs-lookup"><span data-stu-id="95ab0-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="95ab0-256">Это лучше сделать в начале `Invoke` метода по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="95ab0-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="95ab0-257">Это метод обратного вызова, который задает заголовки ответа.</span><span class="sxs-lookup"><span data-stu-id="95ab0-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="95ab0-258">Следующий код задает метод обратного вызова с именем `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="95ab0-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="95ab0-259">`SetHeaders`Метод обратного вызова будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="95ab0-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="95ab0-260">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="95ab0-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="95ab0-261">Файлы cookie передаются в браузер в заголовке ответа *Set-Cookie* .</span><span class="sxs-lookup"><span data-stu-id="95ab0-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="95ab0-262">В результате для отправки файлов cookie требуется тот же обратный вызов, который используется для отправки заголовков ответа:</span><span class="sxs-lookup"><span data-stu-id="95ab0-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="95ab0-263">`SetCookies`Метод обратного вызова будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="95ab0-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="95ab0-264">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="95ab0-264">Additional resources</span></span>

* [<span data-ttu-id="95ab0-265">Общие сведения о HTTP-обработчиках и модулях HTTP</span><span class="sxs-lookup"><span data-stu-id="95ab0-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="95ab0-266">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="95ab0-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="95ab0-267">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="95ab0-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="95ab0-268">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="95ab0-268">Middleware</span></span>](xref:fundamentals/middleware/index)
