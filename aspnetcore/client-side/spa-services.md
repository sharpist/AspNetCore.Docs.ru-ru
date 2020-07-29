---
title: Создание одностраничных приложений в ASP.NET Core с помощью служб JavaScript
author: scottaddie
description: Ознакомьтесь с преимуществами использования служб JavaScript для создания одностраничных приложений на основе ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: client-side/spa-services
ms.openlocfilehash: 05f76a7d341fc5c55b8234b6ff6d2be5aa61d6fd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401835"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="7c76c-103">Создание одностраничных приложений в ASP.NET Core с помощью служб JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c76c-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="7c76c-104">Авторы: [Скотт Адди](https://github.com/scottaddie) (Scott Addie) и [Фияз Хасан](https://fiyazhasan.me/) (Fiyaz Hasan)</span><span class="sxs-lookup"><span data-stu-id="7c76c-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="7c76c-105">Одностраничные приложения (SPA) — это популярный тип веб-приложений из-за присущих им широких возможностей пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="7c76c-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="7c76c-106">Интеграция клиентских платформ и библиотек SPA, таких как [Angular](https://angular.io/) или [React](https://facebook.github.io/react/), с серверными платформами, такими как ASP.NET Core, может представлять сложность.</span><span class="sxs-lookup"><span data-stu-id="7c76c-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="7c76c-107">Чтобы упростить интеграцию, были разработаны службы JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7c76c-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="7c76c-108">Они обеспечивают эффективное взаимодействие между различными стеками клиентских и серверных технологий.</span><span class="sxs-lookup"><span data-stu-id="7c76c-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="7c76c-109">Функции, описанные в этой статье, устарели с версии ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7c76c-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="7c76c-110">В пакете NuGet [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) реализован более простой механизм интеграции с платформами SPA.</span><span class="sxs-lookup"><span data-stu-id="7c76c-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="7c76c-111">Дополнительные сведения см. в [объявлении об устаревании Microsoft.AspNetCore.SpaServices и Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="7c76c-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="7c76c-112">Что такое службы JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c76c-112">What is JavaScript Services</span></span>

<span data-ttu-id="7c76c-113">Службы JavaScript — это набор технологий на стороне клиента для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c76c-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="7c76c-114">Его целью является позиционирование ASP.NET Core в качестве предпочтительной серверной платформы для разработки одностраничных приложений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="7c76c-115">В состав служб JavaScript входят два отдельных пакета NuGet:</span><span class="sxs-lookup"><span data-stu-id="7c76c-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="7c76c-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="7c76c-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="7c76c-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="7c76c-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="7c76c-118">Эти пакеты полезны в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="7c76c-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="7c76c-119">выполнение JavaScript на сервере;</span><span class="sxs-lookup"><span data-stu-id="7c76c-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="7c76c-120">использование платформы или библиотеки SPA;</span><span class="sxs-lookup"><span data-stu-id="7c76c-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="7c76c-121">создание ресурсов на стороне клиента с помощью Webpack.</span><span class="sxs-lookup"><span data-stu-id="7c76c-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="7c76c-122">В этой статье основное внимание уделяется использованию пакета SpaServices.</span><span class="sxs-lookup"><span data-stu-id="7c76c-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="7c76c-123">Что такое SpaServices</span><span class="sxs-lookup"><span data-stu-id="7c76c-123">What is SpaServices</span></span>

<span data-ttu-id="7c76c-124">Пакет SpaServices был создан с целью позиционирования ASP.NET Core в качестве предпочтительной серверной платформы для разработки одностраничных приложений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="7c76c-125">Он не обязателен для разработки одностраничных приложений с помощью ASP.NET Core и не ограничивает выбор разработчиков определенной клиентской платформой.</span><span class="sxs-lookup"><span data-stu-id="7c76c-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="7c76c-126">Пакет SpaServices предоставляет полезные инфраструктурные возможности, в том числе:</span><span class="sxs-lookup"><span data-stu-id="7c76c-126">SpaServices provides useful infrastructure such as:</span></span>

* <span data-ttu-id="7c76c-127">[предварительная обработка на стороне сервера](#server-side-prerendering);</span><span class="sxs-lookup"><span data-stu-id="7c76c-127">[Server-side prerendering](#server-side-prerendering)</span></span>
* <span data-ttu-id="7c76c-128">[ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware);</span><span class="sxs-lookup"><span data-stu-id="7c76c-128">[Webpack Dev Middleware](#webpack-dev-middleware)</span></span>
* <span data-ttu-id="7c76c-129">[горячая замена модулей](#hot-module-replacement);</span><span class="sxs-lookup"><span data-stu-id="7c76c-129">[Hot Module Replacement](#hot-module-replacement)</span></span>
* <span data-ttu-id="7c76c-130">[вспомогательные методы маршрутизации](#routing-helpers).</span><span class="sxs-lookup"><span data-stu-id="7c76c-130">[Routing helpers](#routing-helpers)</span></span>

<span data-ttu-id="7c76c-131">В совокупности эти компоненты инфраструктуры улучшают как процесс разработки, так и выполнение приложений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="7c76c-132">Их можно внедрять по отдельности.</span><span class="sxs-lookup"><span data-stu-id="7c76c-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="7c76c-133">Необходимые условия для использования SpaServices</span><span class="sxs-lookup"><span data-stu-id="7c76c-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="7c76c-134">Для работы со SpaServices установите перечисленные ниже компоненты.</span><span class="sxs-lookup"><span data-stu-id="7c76c-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="7c76c-135">[Node.js](https://nodejs.org/) (версии 6 или более поздней) с npm</span><span class="sxs-lookup"><span data-stu-id="7c76c-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="7c76c-136">Чтобы проверить, установлены ли эти компоненты, выполните в командной строке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7c76c-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="7c76c-137">При развертывании на веб-сайте Azure никаких действий не требуется &mdash; платформа Node.js уже установлена и доступна в серверных средах.</span><span class="sxs-lookup"><span data-stu-id="7c76c-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="7c76c-138">В ОС Windows с Visual Studio 2017 для установки пакета SDK нужно выбрать рабочую нагрузку **Кроссплатформенная разработка .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="7c76c-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="7c76c-139">Пакет NuGet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="7c76c-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="7c76c-140">Предварительная обработка на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="7c76c-140">Server-side prerendering</span></span>

<span data-ttu-id="7c76c-141">Универсальное приложение (также называемое изоморфным) — это приложение JavaScript, которое может выполняться как на сервере, так и в клиенте.</span><span class="sxs-lookup"><span data-stu-id="7c76c-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="7c76c-142">Angular, React и другие популярные платформы обеспечивают возможности для разработки приложений в таком стиле.</span><span class="sxs-lookup"><span data-stu-id="7c76c-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="7c76c-143">Главный принцип заключается в том, что компоненты платформы сначала обрабатываются на сервере с помощью Node.js, после чего передаются в клиент для дальнейшего выполнения.</span><span class="sxs-lookup"><span data-stu-id="7c76c-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="7c76c-144">[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) ASP.NET Core, предоставляемые пакетом SpaServices, упрощают реализацию предварительной обработки на стороне сервера путем вызова функций JavaScript на сервере.</span><span class="sxs-lookup"><span data-stu-id="7c76c-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="7c76c-145">Необходимые условия для предварительной обработки на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="7c76c-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="7c76c-146">Установите пакет npm [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):</span><span class="sxs-lookup"><span data-stu-id="7c76c-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="7c76c-147">Настройка предварительной обработки на стороне сервера</span><span class="sxs-lookup"><span data-stu-id="7c76c-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="7c76c-148">Вспомогательные функции тегов становятся доступны путем регистрации пространства имен в файле *_ViewImports.cshtml* проекта:</span><span class="sxs-lookup"><span data-stu-id="7c76c-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="7c76c-149">Эти функции позволяют абстрагироваться от сложностей, связанных с взаимодействием с низкоуровневыми интерфейсами API напрямую, благодаря использованию синтаксиса в стиле HTML в представлении [Razor:</span><span class="sxs-lookup"><span data-stu-id="7c76c-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the [Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="7c76c-150">Вспомогательная функция тегов asp-prerender-module</span><span class="sxs-lookup"><span data-stu-id="7c76c-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="7c76c-151">Вспомогательная функция тегов `asp-prerender-module`, используемая в предыдущем примере кода, выполняет файл *ClientApp/dist/main-server.js* на сервере с помощью Node.js.</span><span class="sxs-lookup"><span data-stu-id="7c76c-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="7c76c-152">Чтобы было понятно, уточним, что файл *main-server.js* — это артефакт, получаемый в результате выполнения задачи транспилирования TypeScript в JavaScript в процессе сборки [Webpack](https://webpack.github.io/).</span><span class="sxs-lookup"><span data-stu-id="7c76c-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="7c76c-153">В Webpack определен псевдоним точки входа `main-server`. Обход схемы зависимостей для этого псевдонима начинается с файла *ClientApp/boot-server.ts*:</span><span class="sxs-lookup"><span data-stu-id="7c76c-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="7c76c-154">В следующем примере для Angular файл *ClientApp/boot-server.ts* использует функцию `createServerRenderer` и тип `RenderResult` из пакета npm `aspnet-prerendering` для настройки обработки на сервере с помощью Node.js.</span><span class="sxs-lookup"><span data-stu-id="7c76c-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="7c76c-155">Разметка HTML, предназначенная для обработки на стороне сервера, передается в вызов функции resolve, который заключен в строго типизированный объект JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="7c76c-156">Важность объекта `Promise` заключается в том, что он асинхронно передает HTML-разметку странице для внедрения в элемент-заполнитель модели DOM.</span><span class="sxs-lookup"><span data-stu-id="7c76c-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="7c76c-157">Вспомогательная функция тегов asp-prerender-data</span><span class="sxs-lookup"><span data-stu-id="7c76c-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="7c76c-158">При использовании в сочетании с функцией `asp-prerender-module` вспомогательную функцию тегов `asp-prerender-data` можно использовать для передачи контекстных сведений из представления [Razor в код JavaScript на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="7c76c-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the [Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="7c76c-159">Например, следующая разметка передает пользовательские данные в модуль `main-server`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="7c76c-160">Полученный аргумент `UserName` сериализуется с помощью встроенного сериализатора JSON и сохраняется в объекте `params.data`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="7c76c-161">В следующем примере для Angular данные используются для создания персонализированного приветствия в элементе `h1`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="7c76c-162">Имена свойств, передаваемые во вспомогательных функциях тегов, представлены в нотации **РегистрПаскаля**.</span><span class="sxs-lookup"><span data-stu-id="7c76c-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="7c76c-163">В отличие от этого, в JavaScript те же самые имена свойств представлены в **верблюжьем регистре**.</span><span class="sxs-lookup"><span data-stu-id="7c76c-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="7c76c-164">Это различие обуславливается конфигурацией сериализации JSON по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7c76c-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="7c76c-165">Если продолжить предыдущий пример кода, данные можно передать с сервера в представление путем расконсервации свойства `globals`, предоставляемого функции `resolve`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="7c76c-166">Массив `postList`, определенный внутри объекта `globals`, присоединяется к глобальному объекту `window` браузера.</span><span class="sxs-lookup"><span data-stu-id="7c76c-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="7c76c-167">Перевод переменной в глобальную область позволяет избежать лишних усилий, особенно когда одни и те же данные загружаются сначала на сервере, а затем в клиенте.</span><span class="sxs-lookup"><span data-stu-id="7c76c-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![глобальная переменная postList, присоединенная к объекту window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="7c76c-169">ПО промежуточного слоя Webpack для разработки</span><span class="sxs-lookup"><span data-stu-id="7c76c-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="7c76c-170">[ПО промежуточного слоя Webpack для разработки](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) упрощает процесс разработки благодаря сборке ресурсов средством Webpack по запросу.</span><span class="sxs-lookup"><span data-stu-id="7c76c-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="7c76c-171">Оно автоматически компилирует и предоставляет ресурсы на стороне клиента при перезагрузке страницы в браузере.</span><span class="sxs-lookup"><span data-stu-id="7c76c-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="7c76c-172">Альтернативный подход заключается в вызове Webpack вручную через скрипт сборки npm проекта при изменении сторонней зависимости или пользовательского кода.</span><span class="sxs-lookup"><span data-stu-id="7c76c-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="7c76c-173">Скрипт сборки npm в файле *package.json* представлен в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="7c76c-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="7c76c-174">Необходимые условия для использования ПО промежуточного слоя Webpack для разработки</span><span class="sxs-lookup"><span data-stu-id="7c76c-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="7c76c-175">Установите пакет npm [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):</span><span class="sxs-lookup"><span data-stu-id="7c76c-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="7c76c-176">Настройка ПО промежуточного слоя Webpack для разработки</span><span class="sxs-lookup"><span data-stu-id="7c76c-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="7c76c-177">ПО промежуточного слоя Webpack для разработки регистрируется в конвейере HTTP-запросов с помощью следующего кода в методе `Configure` в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7c76c-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="7c76c-178">Метод расширения `UseWebpackDevMiddleware` должен вызываться перед [регистрацией размещения статического файла](xref:fundamentals/static-files) с помощью метода расширения `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="7c76c-179">В целях безопасности регистрировать ПО промежуточного слоя следует только при запуске приложения в режиме разработки.</span><span class="sxs-lookup"><span data-stu-id="7c76c-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="7c76c-180">Свойство `output.publicPath` в файле *webpack.config.js* предписывает ПО промежуточного слоя отслеживать изменения в папке `dist`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="7c76c-181">Горячая замена модулей</span><span class="sxs-lookup"><span data-stu-id="7c76c-181">Hot Module Replacement</span></span>

<span data-ttu-id="7c76c-182">Функцию [горячей замены модулей](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) в Webpack можно рассматривать как дальнейшее развитие [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="7c76c-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="7c76c-183">HMR дает те же преимущества, но еще более упрощает процесс разработки благодаря автоматическому обновлению содержимого страницы после компиляции изменений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="7c76c-184">Не путайте это с обновлением содержимого браузера, которое нарушает текущее состояние в памяти и сеанс отладки приложения SPA.</span><span class="sxs-lookup"><span data-stu-id="7c76c-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="7c76c-185">Между службой ПО промежуточного слоя Webpack для разработки и браузером существует динамическая связь, благодаря которой изменения передаются в браузер.</span><span class="sxs-lookup"><span data-stu-id="7c76c-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="7c76c-186">Необходимые условия для горячей замены модулей</span><span class="sxs-lookup"><span data-stu-id="7c76c-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="7c76c-187">Установите пакет npm [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):</span><span class="sxs-lookup"><span data-stu-id="7c76c-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="7c76c-188">Настройка горячей замены модулей</span><span class="sxs-lookup"><span data-stu-id="7c76c-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="7c76c-189">Компонент HMR необходимо зарегистрировать в конвейере HTTP-запросов MVC в методе `Configure`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="7c76c-190">Так же как и в случае с [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware), метод расширения `UseWebpackDevMiddleware` должен вызываться до метода расширения `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="7c76c-191">В целях безопасности регистрировать ПО промежуточного слоя следует только при запуске приложения в режиме разработки.</span><span class="sxs-lookup"><span data-stu-id="7c76c-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="7c76c-192">В файле *webpack.config.js* должен определяться массив `plugins`, даже если он будет пустым:</span><span class="sxs-lookup"><span data-stu-id="7c76c-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="7c76c-193">После загрузки приложения в браузере на вкладке "Консоль" средств разработчика выводится подтверждение активации HMR.</span><span class="sxs-lookup"><span data-stu-id="7c76c-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Сообщение о подключении функции горячей замены модулей](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="7c76c-195">Вспомогательные методы маршрутизации</span><span class="sxs-lookup"><span data-stu-id="7c76c-195">Routing helpers</span></span>

<span data-ttu-id="7c76c-196">В большинстве одностраничных приложений на основе ASP.NET Core, помимо маршрутизации на стороне сервера, часто требуется маршрутизация на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7c76c-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="7c76c-197">Системы маршрутизации SPA и MVC могут работать независимо, не мешая друг другу.</span><span class="sxs-lookup"><span data-stu-id="7c76c-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="7c76c-198">Однако существует один пограничный случай, вызывающий трудности: идентификация HTTP-ответов 404.</span><span class="sxs-lookup"><span data-stu-id="7c76c-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="7c76c-199">Рассмотрим ситуацию, в которой используется маршрут `/some/page` без расширений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="7c76c-200">Предположим, что шаблон запроса не соответствует маршруту на стороне сервера, но соответствует маршруту на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7c76c-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="7c76c-201">Теперь предположим, что поступил запрос пути `/images/user-512.png`. Такой запрос обычно служит для поиска файла изображения на сервере.</span><span class="sxs-lookup"><span data-stu-id="7c76c-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="7c76c-202">Если запрошенный путь к ресурсу не соответствует ни одному маршруту на стороне сервера или статическому файлу, то маловероятно, что клиентское приложение будет его обрабатывать &mdash; обычно в таких случаях возвращается код состояния HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="7c76c-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="7c76c-203">Необходимые условия для использования вспомогательных методов маршрутизации</span><span class="sxs-lookup"><span data-stu-id="7c76c-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="7c76c-204">Установите пакет npm для маршрутизации на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7c76c-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="7c76c-205">Вот пример для Angular:</span><span class="sxs-lookup"><span data-stu-id="7c76c-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="7c76c-206">Настройка вспомогательных методов маршрутизации</span><span class="sxs-lookup"><span data-stu-id="7c76c-206">Routing helpers configuration</span></span>

<span data-ttu-id="7c76c-207">В методе `Configure` используется метод расширения `MapSpaFallbackRoute`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="7c76c-208">Маршруты оцениваются в той очередности, в которой они были настроены.</span><span class="sxs-lookup"><span data-stu-id="7c76c-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="7c76c-209">Поэтому в предыдущем примере кода для сопоставления шаблонов сначала используется маршрут `default`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="7c76c-210">Создание нового проекта</span><span class="sxs-lookup"><span data-stu-id="7c76c-210">Create a new project</span></span>

<span data-ttu-id="7c76c-211">Службы JavaScript предоставляют предварительно настроенные шаблоны приложений.</span><span class="sxs-lookup"><span data-stu-id="7c76c-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="7c76c-212">Пакет SpaServices используется в них в сочетании с различными платформами и библиотеками, таким как Angular, React и Redux.</span><span class="sxs-lookup"><span data-stu-id="7c76c-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="7c76c-213">Эти шаблоны можно установить с помощью .NET Core CLI, выполнив следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7c76c-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="7c76c-214">Отобразится список доступных шаблонов SPA.</span><span class="sxs-lookup"><span data-stu-id="7c76c-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="7c76c-215">Шаблоны</span><span class="sxs-lookup"><span data-stu-id="7c76c-215">Templates</span></span>                                 | <span data-ttu-id="7c76c-216">Краткое имя</span><span class="sxs-lookup"><span data-stu-id="7c76c-216">Short Name</span></span> | <span data-ttu-id="7c76c-217">Язык</span><span class="sxs-lookup"><span data-stu-id="7c76c-217">Language</span></span> | <span data-ttu-id="7c76c-218">Tags</span><span class="sxs-lookup"><span data-stu-id="7c76c-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="7c76c-219">MVC ASP.NET Core с Angular</span><span class="sxs-lookup"><span data-stu-id="7c76c-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="7c76c-220">angular</span><span class="sxs-lookup"><span data-stu-id="7c76c-220">angular</span></span>    | <span data-ttu-id="7c76c-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="7c76c-221">[C#]</span></span>     | <span data-ttu-id="7c76c-222">MVC/Веб/SPA</span><span class="sxs-lookup"><span data-stu-id="7c76c-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="7c76c-223">MVC ASP.NET Core с React.js</span><span class="sxs-lookup"><span data-stu-id="7c76c-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="7c76c-224">react</span><span class="sxs-lookup"><span data-stu-id="7c76c-224">react</span></span>      | <span data-ttu-id="7c76c-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="7c76c-225">[C#]</span></span>     | <span data-ttu-id="7c76c-226">MVC/Веб/SPA</span><span class="sxs-lookup"><span data-stu-id="7c76c-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="7c76c-227">MVC ASP.NET Core с React.js и Redux</span><span class="sxs-lookup"><span data-stu-id="7c76c-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="7c76c-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="7c76c-228">reactredux</span></span> | <span data-ttu-id="7c76c-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="7c76c-229">[C#]</span></span>     | <span data-ttu-id="7c76c-230">MVC/Веб/SPA</span><span class="sxs-lookup"><span data-stu-id="7c76c-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="7c76c-231">Чтобы создать проект с помощью одного из шаблонов SPA, включите **короткое имя** шаблона в команду [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="7c76c-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="7c76c-232">Следующая команда создает приложение Angular с MVC ASP.NET Core, настроенное как серверное:</span><span class="sxs-lookup"><span data-stu-id="7c76c-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="7c76c-233">Задание режима конфигурации среды выполнения</span><span class="sxs-lookup"><span data-stu-id="7c76c-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="7c76c-234">Существует два основных режима конфигурации среды выполнения:</span><span class="sxs-lookup"><span data-stu-id="7c76c-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="7c76c-235">**Разработка**:</span><span class="sxs-lookup"><span data-stu-id="7c76c-235">**Development**:</span></span>
  * <span data-ttu-id="7c76c-236">включает сопоставители с исходным кодом для упрощения отладки;</span><span class="sxs-lookup"><span data-stu-id="7c76c-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="7c76c-237">не оптимизирует производительность кода на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7c76c-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="7c76c-238">**Рабочая среда**:</span><span class="sxs-lookup"><span data-stu-id="7c76c-238">**Production**:</span></span>
  * <span data-ttu-id="7c76c-239">исключает сопоставители с исходным кодом;</span><span class="sxs-lookup"><span data-stu-id="7c76c-239">Excludes source maps.</span></span>
  * <span data-ttu-id="7c76c-240">оптимизирует код на стороне клиента посредством объединения и минификации.</span><span class="sxs-lookup"><span data-stu-id="7c76c-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="7c76c-241">В ASP.NET Core режим конфигурации хранится в переменной среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="7c76c-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="7c76c-242">Дополнительные сведения см. в разделе [Указание среды](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="7c76c-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="7c76c-243">Запуск с помощью .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7c76c-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="7c76c-244">Восстановите необходимые пакеты NuGet и npm, выполнив следующую команду в корневом каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="7c76c-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="7c76c-245">Выполните сборку и запуск приложения:</span><span class="sxs-lookup"><span data-stu-id="7c76c-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="7c76c-246">Приложение запускается на узле localhost в соответствии с [режимом конфигурации среды выполнения](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="7c76c-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="7c76c-247">При переходе по адресу `http://localhost:5000` в браузере открывается целевая страница.</span><span class="sxs-lookup"><span data-stu-id="7c76c-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="7c76c-248">Запуск с помощью Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="7c76c-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="7c76c-249">Откройте файл *CSPROJ*, созданный с помощью команды [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="7c76c-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="7c76c-250">Необходимые пакеты NuGet и npm восстанавливаются автоматически при открытии проекта:</span><span class="sxs-lookup"><span data-stu-id="7c76c-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="7c76c-251">Процесс восстановления может занять несколько минут. По его завершении приложение будет готово к запуску.</span><span class="sxs-lookup"><span data-stu-id="7c76c-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="7c76c-252">Нажмите зеленую кнопку запуска или клавиши `Ctrl + F5`, и в браузере откроется целевая страница приложения.</span><span class="sxs-lookup"><span data-stu-id="7c76c-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="7c76c-253">Приложение запускается на узле localhost в соответствии с [режимом конфигурации среды выполнения](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="7c76c-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="7c76c-254">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="7c76c-254">Test the app</span></span>

<span data-ttu-id="7c76c-255">В шаблонах SpaServices предварительно настроено выполнение тестов на стороне клиента с помощью [Karma](https://karma-runner.github.io/1.0/index.html) и [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="7c76c-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="7c76c-256">Jasmine — это популярная платформа модульного тестирования для JavaScript, а Karma — это средство запуска тестов.</span><span class="sxs-lookup"><span data-stu-id="7c76c-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="7c76c-257">Средство Karma настроено для работы с [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware), так что разработчику не нужно останавливать и снова запускать тест каждый раз, когда вносится изменение.</span><span class="sxs-lookup"><span data-stu-id="7c76c-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="7c76c-258">Независимо от того, выполняется ли сам тестовый случай или код для тестового случая, тест проводится автоматически.</span><span class="sxs-lookup"><span data-stu-id="7c76c-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="7c76c-259">Возьмем в качестве примера приложение Angular. В файле *counter.component.spec.ts* уже имеется два тестовых случая Jasmine для `CounterComponent`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="7c76c-260">Откройте командную строку в каталоге *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="7c76c-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="7c76c-261">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7c76c-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="7c76c-262">Скрипт запускает средство Karma, которое считывает параметры, определенные в файле *karma.conf.js*.</span><span class="sxs-lookup"><span data-stu-id="7c76c-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="7c76c-263">Помимо прочих параметров, в файле *karma.conf.js* определены тестовые файлы, которые должны выполняться для массива `files`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="7c76c-264">Публикация приложения</span><span class="sxs-lookup"><span data-stu-id="7c76c-264">Publish the app</span></span>

<span data-ttu-id="7c76c-265">Дополнительные сведения о публикации в Azure см. в [описании этой проблемы в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474).</span><span class="sxs-lookup"><span data-stu-id="7c76c-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="7c76c-266">Объединение созданных клиентских ресурсов и опубликованных артефактов ASP.NET Core в пакет, готовый к развертыванию, может быть непростой задачей.</span><span class="sxs-lookup"><span data-stu-id="7c76c-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="7c76c-267">К счастью, пакет SpaServices координирует весь процесс публикации с помощью настраиваемого целевого объекта MSBuild с именем `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="7c76c-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="7c76c-268">Целевой объект MSBuild отвечает за следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="7c76c-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="7c76c-269">восстановление пакетов npm;</span><span class="sxs-lookup"><span data-stu-id="7c76c-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="7c76c-270">создание сборки сторонних клиентских ресурсов для рабочей среды;</span><span class="sxs-lookup"><span data-stu-id="7c76c-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="7c76c-271">создание сборки пользовательских клиентских ресурсов для рабочей среды;</span><span class="sxs-lookup"><span data-stu-id="7c76c-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="7c76c-272">копирование ресурсов, созданных средством Webpack, в папку публикации.</span><span class="sxs-lookup"><span data-stu-id="7c76c-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="7c76c-273">Целевой объект MSBuild вызывается при выполнении следующей команды:</span><span class="sxs-lookup"><span data-stu-id="7c76c-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="7c76c-274">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7c76c-274">Additional resources</span></span>

* [<span data-ttu-id="7c76c-275">Документация Angular</span><span class="sxs-lookup"><span data-stu-id="7c76c-275">Angular Docs</span></span>](https://angular.io/docs)
