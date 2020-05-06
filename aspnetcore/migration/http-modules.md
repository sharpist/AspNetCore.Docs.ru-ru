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
ms.openlocfilehash: c2b49976d2063679eab2403aae432660e8c8932d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775418"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="784ae-102">Миграция обработчиков и модулей HTTP в ASP.NET Core по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="784ae-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="784ae-103">В этой статье показано, как перенести существующие [модули HTTP ASP.NET и обработчики с сервера System.](/iis/configuration/system.webserver/) в ASP.NET Core по [промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="784ae-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="784ae-104">Повторно посещаемые модули и обработчики</span><span class="sxs-lookup"><span data-stu-id="784ae-104">Modules and handlers revisited</span></span>

<span data-ttu-id="784ae-105">Прежде чем перейти к ASP.NET Core по промежуточного слоя, давайте сначала посмотрим, как работают HTTP-модули и обработчики:</span><span class="sxs-lookup"><span data-stu-id="784ae-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Обработчик модулей](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="784ae-107">**Обработчики:**</span><span class="sxs-lookup"><span data-stu-id="784ae-107">**Handlers are:**</span></span>

* <span data-ttu-id="784ae-108">Классы, реализующие [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="784ae-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="784ae-109">Используется для обработки запросов с заданным именем или расширением файла, например *. Report*</span><span class="sxs-lookup"><span data-stu-id="784ae-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="784ae-110">[Настроено](/iis/configuration/system.webserver/handlers/) в *файле Web. config*</span><span class="sxs-lookup"><span data-stu-id="784ae-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="784ae-111">**Модули:**</span><span class="sxs-lookup"><span data-stu-id="784ae-111">**Modules are:**</span></span>

* <span data-ttu-id="784ae-112">Классы, реализующие интерфейс [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="784ae-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="784ae-113">Вызывается для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="784ae-113">Invoked for every request</span></span>

* <span data-ttu-id="784ae-114">Возможность краткого замыкания (завершение дальнейшей обработки запроса)</span><span class="sxs-lookup"><span data-stu-id="784ae-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="784ae-115">Возможность добавления в HTTP-ответ или создания собственных</span><span class="sxs-lookup"><span data-stu-id="784ae-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="784ae-116">[Настроено](/iis/configuration/system.webserver/modules/) в *файле Web. config*</span><span class="sxs-lookup"><span data-stu-id="784ae-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="784ae-117">**Порядок, в котором модули обрабатывают входящие запросы, определяется следующим образом.**</span><span class="sxs-lookup"><span data-stu-id="784ae-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="784ae-118">[Жизненный цикл приложения](https://msdn.microsoft.com/library/ms227673.aspx)— это события серии, запускаемые ASP.NET: [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)и т. д. Каждый модуль может создать обработчик для одного или нескольких событий.</span><span class="sxs-lookup"><span data-stu-id="784ae-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="784ae-119">Для одного и того же события — порядок, в котором они настроены в *файле Web. config*.</span><span class="sxs-lookup"><span data-stu-id="784ae-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="784ae-120">В дополнение к модулям можно добавлять обработчики для событий жизненного цикла в файл *Global.asax.CS* .</span><span class="sxs-lookup"><span data-stu-id="784ae-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="784ae-121">Эти обработчики запускаются после обработчиков в настроенных модулях.</span><span class="sxs-lookup"><span data-stu-id="784ae-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="784ae-122">От обработчиков и модулей до по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="784ae-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="784ae-123">**По промежуточного слоя проще, чем модули HTTP и обработчики:**</span><span class="sxs-lookup"><span data-stu-id="784ae-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="784ae-124">Модули, обработчики, *Global.asax.CS*, *Web. config* (кроме конфигурации IIS) и жизненный цикл приложения исчезают</span><span class="sxs-lookup"><span data-stu-id="784ae-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="784ae-125">Роли обоих модулей и обработчиков были взяты по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="784ae-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="784ae-126">По промежуточного слоя настраивается с использованием кода, а не в *файле Web. config*</span><span class="sxs-lookup"><span data-stu-id="784ae-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="784ae-127">[Ветвление конвейера](xref:fundamentals/middleware/index#use-run-and-map) позволяет отправлять запросы к определенному по промежуточного слоя, основываясь не только на URL-адресе, но также и в заголовках запросов, строках запросов и т. д.</span><span class="sxs-lookup"><span data-stu-id="784ae-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="784ae-128">**По промежуточного слоя очень похоже на модули:**</span><span class="sxs-lookup"><span data-stu-id="784ae-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="784ae-129">Вызывается в принципе для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="784ae-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="784ae-130">Возможность сокращения запроса путем [передачи запроса следующему по промежуточного слоя](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="784ae-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="784ae-131">Возможность создания собственного HTTP-ответа</span><span class="sxs-lookup"><span data-stu-id="784ae-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="784ae-132">**По промежуточного слоя и модули обрабатываются в другом порядке:**</span><span class="sxs-lookup"><span data-stu-id="784ae-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="784ae-133">Порядок по промежуточного слоя основан на порядке, в котором они вставляются в конвейер запросов, а порядок модулей в основном основан на событиях [жизненного цикла приложения](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="784ae-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="784ae-134">Порядок по промежуточного слоя для ответов является обратным по отношению к запросам, а порядок модулей одинаков для запросов и ответов</span><span class="sxs-lookup"><span data-stu-id="784ae-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="784ae-135">См. раздел [создание конвейера по промежуточного слоя с помощью IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) .</span><span class="sxs-lookup"><span data-stu-id="784ae-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![ПО промежуточного слоя](http-modules/_static/middleware.png)

<span data-ttu-id="784ae-137">Обратите внимание на то, как на приведенном выше рисунке промежуточное по проверки подлинности сокращено по запросу.</span><span class="sxs-lookup"><span data-stu-id="784ae-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="784ae-138">Перенос кода модуля на промежуточное по</span><span class="sxs-lookup"><span data-stu-id="784ae-138">Migrating module code to middleware</span></span>

<span data-ttu-id="784ae-139">Существующий HTTP-модуль будет выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="784ae-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="784ae-140">Как показано на странице по [промежуточного слоя](xref:fundamentals/middleware/index) , ASP.NET Core по промежуточного слоя представляет собой `Invoke` класс, который `HttpContext` предоставляет метод, `Task`принимающий и возвращающий.</span><span class="sxs-lookup"><span data-stu-id="784ae-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="784ae-141">Новое по промежуточного слоя будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="784ae-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="784ae-142">Предыдущий шаблон по промежуточного слоя был взят из раздела о [создании по промежуточного слоя](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="784ae-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="784ae-143">Вспомогательный класс *мимиддлеварикстенсионс* упрощает настройку по промежуточного слоя в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="784ae-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="784ae-144">`UseMyMiddleware` Метод добавляет ваш класс по промежуточного слоя в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="784ae-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="784ae-145">Службы, необходимые по промежуточного слоя, вставляются в конструктор по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="784ae-146">Ваш модуль может завершить запрос, например, если пользователь не является полномочным:</span><span class="sxs-lookup"><span data-stu-id="784ae-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="784ae-147">По промежуточного слоя обрабатывает это, `Invoke` не вызывая для следующего по промежуточного слоя в конвейере.</span><span class="sxs-lookup"><span data-stu-id="784ae-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="784ae-148">Помните, что это не полностью завершает запрос, поскольку предыдущие промежуточные по по-прежнему будут вызываться, когда ответ обратно проходит через конвейер.</span><span class="sxs-lookup"><span data-stu-id="784ae-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="784ae-149">При переносе функциональных возможностей модуля в новое по промежуточного слоя может оказаться, что код не компилируется, поскольку `HttpContext` класс сильно изменился в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="784ae-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="784ae-150">[Позже](#migrating-to-the-new-httpcontext)вы увидите, как перейти на новый ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="784ae-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="784ae-151">Перенос вставки модуля в конвейер запросов</span><span class="sxs-lookup"><span data-stu-id="784ae-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="784ae-152">Модули HTTP обычно добавляются в конвейер запросов с помощью *файла Web. config*:</span><span class="sxs-lookup"><span data-stu-id="784ae-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="784ae-153">Преобразуйте это, [добавив новое по промежуточного слоя](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в конвейер `Startup` запросов в своем классе:</span><span class="sxs-lookup"><span data-stu-id="784ae-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="784ae-154">Точное место в конвейере, где вы вставляете новое по промежуточного слоя, зависит от события, которое оно обрабатывает`BeginRequest`как `EndRequest`модуль (, и т. д.) и его порядок в списке модулей в *файле Web. config*.</span><span class="sxs-lookup"><span data-stu-id="784ae-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="784ae-155">Как было сказано выше, в ASP.NET Core нет жизненного цикла приложения, и порядок обработки ответов по промежуточного слоя отличается от порядка, используемого модулями.</span><span class="sxs-lookup"><span data-stu-id="784ae-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="784ae-156">Это может сделать решение по упорядочиванию более сложным.</span><span class="sxs-lookup"><span data-stu-id="784ae-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="784ae-157">Если заказ станет проблемой, модуль можно разделить на несколько компонентов по промежуточного слоя, которые могут быть упорядочены независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="784ae-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="784ae-158">Перенос кода обработчика в по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="784ae-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="784ae-159">Обработчик HTTP выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="784ae-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="784ae-160">В проекте ASP.NET Core это будет по промежуточного слоя, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="784ae-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="784ae-161">Это по промежуточного слоя очень похоже на по промежуточного слоя, соответствующее модулям.</span><span class="sxs-lookup"><span data-stu-id="784ae-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="784ae-162">Единственная реальная разница заключается в том, что здесь нет вызова `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="784ae-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="784ae-163">Это имеет смысл, так как обработчик находится в конце конвейера запросов, поэтому следующее промежуточное по не будет вызываться.</span><span class="sxs-lookup"><span data-stu-id="784ae-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="784ae-164">Миграция вставки обработчика в конвейер запросов</span><span class="sxs-lookup"><span data-stu-id="784ae-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="784ae-165">Настройка обработчика HTTP выполняется в *файле Web. config* и выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="784ae-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="784ae-166">Это можно преобразовать, добавив новое по промежуточного слоя обработчика в конвейер запросов `Startup` в классе, как и по промежуточного слоя, преобразованного из модулей.</span><span class="sxs-lookup"><span data-stu-id="784ae-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="784ae-167">Проблема этого подхода заключается в том, что она будет отсылать все запросы на новый по промежуточного слоя обработчика.</span><span class="sxs-lookup"><span data-stu-id="784ae-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="784ae-168">Однако запросы с заданным расширением должны достигать вашего по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="784ae-169">Это позволит получить те же функциональные возможности, что и обработчик HTTP.</span><span class="sxs-lookup"><span data-stu-id="784ae-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="784ae-170">Одним из решений является ветвление конвейера для запросов с заданным расширением с помощью метода `MapWhen` расширения.</span><span class="sxs-lookup"><span data-stu-id="784ae-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="784ae-171">Это делается в том же `Configure` методе, в котором вы добавляете другое по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="784ae-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="784ae-172">`MapWhen`принимает следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="784ae-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="784ae-173">Лямбда-выражение, которое `HttpContext` принимает и `true` возвращает, если запрос должен проходить по ветке.</span><span class="sxs-lookup"><span data-stu-id="784ae-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="784ae-174">Это означает, что запросы ветвления можно выполнять не только на основе расширения, но и в заголовках запросов, параметрах строки запроса и т. д.</span><span class="sxs-lookup"><span data-stu-id="784ae-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="784ae-175">Лямбда-выражение, которое `IApplicationBuilder` принимает и добавляет все по промежуточного слоя для ветви.</span><span class="sxs-lookup"><span data-stu-id="784ae-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="784ae-176">Это означает, что можно добавить дополнительное промежуточное по в ветвь перед по промежуточного слоя обработчика.</span><span class="sxs-lookup"><span data-stu-id="784ae-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="784ae-177">По промежуточного слоя, добавленное в конвейер до вызова ветви для всех запросов; ветвь не будет оказывать влияния на них.</span><span class="sxs-lookup"><span data-stu-id="784ae-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="784ae-178">Загрузка параметров по промежуточного слоя с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="784ae-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="784ae-179">Некоторые модули и обработчики имеют параметры конфигурации, которые хранятся в *файле Web. config*. Однако в ASP.NET Core вместо *Web. config*используется новая модель конфигурации.</span><span class="sxs-lookup"><span data-stu-id="784ae-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="784ae-180">Новая [система конфигурации](xref:fundamentals/configuration/index) предоставляет следующие возможности для решения этой задачи:</span><span class="sxs-lookup"><span data-stu-id="784ae-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="784ae-181">Непосредственно внедрять параметры в по промежуточного слоя, как показано в [следующем разделе](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="784ae-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="784ae-182">Используйте [шаблон параметров](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="784ae-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="784ae-183">Создайте класс для хранения параметров по промежуточного слоя, например:</span><span class="sxs-lookup"><span data-stu-id="784ae-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="784ae-184">Сохранение значений параметров</span><span class="sxs-lookup"><span data-stu-id="784ae-184">Store the option values</span></span>

   <span data-ttu-id="784ae-185">Система конфигурации позволяет хранить значения параметров в любом месте.</span><span class="sxs-lookup"><span data-stu-id="784ae-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="784ae-186">Однако большинство сайтов используют *appSettings. JSON*, поэтому мы будем использовать такой подход:</span><span class="sxs-lookup"><span data-stu-id="784ae-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="784ae-187">*Мимиддлевареоптионссектион* здесь — это имя раздела.</span><span class="sxs-lookup"><span data-stu-id="784ae-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="784ae-188">Оно не должно совпадать с именем класса параметров.</span><span class="sxs-lookup"><span data-stu-id="784ae-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="784ae-189">Связывание значений параметров с классом Options</span><span class="sxs-lookup"><span data-stu-id="784ae-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="784ae-190">Шаблон параметров использует инфраструктуру внедрения зависимостей ASP.NET Core для связывания типа параметров (например, `MyMiddlewareOptions`) с `MyMiddlewareOptions` объектом с фактическими параметрами.</span><span class="sxs-lookup"><span data-stu-id="784ae-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="784ae-191">Обновите `Startup` свой класс:</span><span class="sxs-lookup"><span data-stu-id="784ae-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="784ae-192">Если вы используете *appSettings. JSON*, добавьте его в построитель конфигураций в `Startup` конструкторе:</span><span class="sxs-lookup"><span data-stu-id="784ae-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="784ae-193">Настройте службу Options:</span><span class="sxs-lookup"><span data-stu-id="784ae-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="784ae-194">Свяжите свои параметры с классом Options:</span><span class="sxs-lookup"><span data-stu-id="784ae-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="784ae-195">Вставьте параметры в конструктор по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="784ae-196">Это похоже на внедрение параметров в контроллер.</span><span class="sxs-lookup"><span data-stu-id="784ae-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="784ae-197">Метод расширения [усемиддлеваре](#http-modules-usemiddleware) , который добавляет по промежуточного слоя `IApplicationBuilder` в, отвечает за внедрение зависимостей.</span><span class="sxs-lookup"><span data-stu-id="784ae-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="784ae-198">Это не ограничивается `IOptions` объектами.</span><span class="sxs-lookup"><span data-stu-id="784ae-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="784ae-199">Любой другой объект, который требуется по промежуточного слоя, можно внедрить таким образом.</span><span class="sxs-lookup"><span data-stu-id="784ae-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="784ae-200">Загрузка параметров по промежуточного слоя через прямую вставку</span><span class="sxs-lookup"><span data-stu-id="784ae-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="784ae-201">Шаблон параметров имеет преимущество, что он создает слабую связь между значениями параметров и их потребителями.</span><span class="sxs-lookup"><span data-stu-id="784ae-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="784ae-202">После связывания класса Options с фактическими значениями параметров любой другой класс может получить доступ к параметрам через платформу внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="784ae-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="784ae-203">Нет необходимости передавать значения параметров.</span><span class="sxs-lookup"><span data-stu-id="784ae-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="784ae-204">Это разбивается, если вы хотите использовать одно по промежуточного слоя дважды с различными параметрами.</span><span class="sxs-lookup"><span data-stu-id="784ae-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="784ae-205">Например, по промежуточного слоя авторизации, используемого в разных ветвях, которые позволяют использовать разные роли.</span><span class="sxs-lookup"><span data-stu-id="784ae-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="784ae-206">Нельзя связать два различных объекта Options с одним классом параметров.</span><span class="sxs-lookup"><span data-stu-id="784ae-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="784ae-207">Решение заключается в получении объектов Options с фактическими значениями параметров в `Startup` классе и передачей их непосредственно в каждый экземпляр по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="784ae-208">Добавьте второй ключ в *appSettings. JSON.*</span><span class="sxs-lookup"><span data-stu-id="784ae-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="784ae-209">Чтобы добавить второй набор параметров в файл *appSettings. JSON* , используйте новый ключ для уникальной идентификации.</span><span class="sxs-lookup"><span data-stu-id="784ae-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="784ae-210">Извлеките значения параметров и передайте их в по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="784ae-211">Метод `Use...` расширения (который добавляет свое по промежуточного слоя в конвейер) является логическим местом для передачи значений параметров:</span><span class="sxs-lookup"><span data-stu-id="784ae-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="784ae-212">Включение по промежуточного слоя для использования параметра options.</span><span class="sxs-lookup"><span data-stu-id="784ae-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="784ae-213">Предоставьте перегрузку метода `Use...` расширения (который принимает параметр options и передает его в `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="784ae-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="784ae-214">Когда `UseMiddleware` вызывается с параметрами, он передает параметры в конструктор по промежуточного слоя при создании экземпляра объекта по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="784ae-215">Обратите внимание, что это создает оболочку для `OptionsWrapper` объекта Options в объекте.</span><span class="sxs-lookup"><span data-stu-id="784ae-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="784ae-216">В этом `IOptions`случае реализуется, как и ожидается конструктором по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="784ae-217">Переход на новый элемент HttpContext</span><span class="sxs-lookup"><span data-stu-id="784ae-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="784ae-218">Ранее было показано, что `Invoke` метод в по промежуточного слоя принимает параметр типа `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="784ae-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="784ae-219">`HttpContext`значительно изменился в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="784ae-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="784ae-220">В этом разделе показано, как преобразовать наиболее часто используемые свойства [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) в новый `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="784ae-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="784ae-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="784ae-221">HttpContext</span></span>

<span data-ttu-id="784ae-222">**Объекты HttpContext. Items** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="784ae-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="784ae-223">**Уникальный идентификатор запроса (не является аналогом System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="784ae-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="784ae-224">Предоставляет уникальный идентификатор для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="784ae-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="784ae-225">Очень полезно включить в журналы.</span><span class="sxs-lookup"><span data-stu-id="784ae-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="784ae-226">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="784ae-226">HttpContext.Request</span></span>

<span data-ttu-id="784ae-227">**HttpContext. Request. HttpMethod** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="784ae-228">**Запрос HttpContext. Request. QueryString** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="784ae-229">**HttpContext. Request. URL** и **HttpContext. Request. равурл** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="784ae-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="784ae-230">**HttpContext. Request. иссекуреконнектион** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="784ae-231">**HttpContext. Request. усерхостаддресс** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="784ae-232">**Запросы HttpContext. Request. cookie** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="784ae-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="784ae-233">**Запрос HttpContext. Request. RequestContext. RouteData** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="784ae-234">**Запросы HttpContext. Request. Headers** преобразуются в:</span><span class="sxs-lookup"><span data-stu-id="784ae-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="784ae-235">**HttpContext. Request. UserAgent** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="784ae-236">**HttpContext. Request. урлреферрер** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="784ae-237">Преобразование **HttpContext. Request. ContentType** в:</span><span class="sxs-lookup"><span data-stu-id="784ae-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="784ae-238">Преобразование **HttpContext. Request. Form** в:</span><span class="sxs-lookup"><span data-stu-id="784ae-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="784ae-239">Чтение значений формы происходит только в том случае, если подтип содержимого имеет вид *x-www-Form-UrlEncoded* или *form-data*.</span><span class="sxs-lookup"><span data-stu-id="784ae-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="784ae-240">**HttpContext. Request. InputStream** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="784ae-241">Этот код следует использовать только в по промежуточного слоя типа обработчика в конце конвейера.</span><span class="sxs-lookup"><span data-stu-id="784ae-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="784ae-242">Вы можете прочитать необработанный текст, как показано выше, для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="784ae-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="784ae-243">По промежуточного слоя, пытающегося прочитать текст после первого чтения, будет считать пустое тело.</span><span class="sxs-lookup"><span data-stu-id="784ae-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="784ae-244">Это не относится к чтению формы, как показано выше, так как это делается из буфера.</span><span class="sxs-lookup"><span data-stu-id="784ae-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="784ae-245">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="784ae-245">HttpContext.Response</span></span>

<span data-ttu-id="784ae-246">**HttpContext. Response. status** и **HttpContext. Response. StatusDescription** транслируются в:</span><span class="sxs-lookup"><span data-stu-id="784ae-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="784ae-247">Преобразование **HttpContext. Response. ContentEncoding** и **HttpContext. Response. ContentType** в:</span><span class="sxs-lookup"><span data-stu-id="784ae-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="784ae-248">**HttpContext. Response. ContentType** также преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="784ae-249">**HttpContext. Response. Output** преобразуется в:</span><span class="sxs-lookup"><span data-stu-id="784ae-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="784ae-250">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="784ae-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="784ae-251">[Здесь](../fundamentals/request-features.md#middleware-and-request-features)обсуждается обслуживание файла.</span><span class="sxs-lookup"><span data-stu-id="784ae-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="784ae-252">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="784ae-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="784ae-253">Отправка заголовков ответа осложняется тем, что если вы задали их после того, как что-либо записало в текст ответа, они не будут отправлены.</span><span class="sxs-lookup"><span data-stu-id="784ae-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="784ae-254">Решением является установка метода обратного вызова, который будет вызываться прямо перед началом записи в ответ.</span><span class="sxs-lookup"><span data-stu-id="784ae-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="784ae-255">Это лучше сделать в начале `Invoke` метода по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="784ae-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="784ae-256">Это метод обратного вызова, который задает заголовки ответа.</span><span class="sxs-lookup"><span data-stu-id="784ae-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="784ae-257">Следующий код задает метод обратного вызова с `SetHeaders`именем:</span><span class="sxs-lookup"><span data-stu-id="784ae-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="784ae-258">Метод `SetHeaders` обратного вызова будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="784ae-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="784ae-259">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="784ae-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="784ae-260">Файлы cookie передаются в браузер в заголовке ответа *Set-Cookie* .</span><span class="sxs-lookup"><span data-stu-id="784ae-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="784ae-261">В результате для отправки файлов cookie требуется тот же обратный вызов, который используется для отправки заголовков ответа:</span><span class="sxs-lookup"><span data-stu-id="784ae-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="784ae-262">Метод `SetCookies` обратного вызова будет выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="784ae-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="784ae-263">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="784ae-263">Additional resources</span></span>

* [<span data-ttu-id="784ae-264">Общие сведения о HTTP-обработчиках и модулях HTTP</span><span class="sxs-lookup"><span data-stu-id="784ae-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="784ae-265">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="784ae-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="784ae-266">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="784ae-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="784ae-267">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="784ae-267">Middleware</span></span>](xref:fundamentals/middleware/index)
