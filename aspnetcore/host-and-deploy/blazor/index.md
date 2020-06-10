---
title: Размещение и развертывание ASP.NET Core Blazor
author: guardrex
description: Узнайте, как размещать и развертывать приложения Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 482b066d347c2c572b0ffb78ccab1ac391195823
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452243"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="7267d-103">Размещение и развертывание ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7267d-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="7267d-104">Авторы: [Люк Лэтем](https://github.com/guardrex), [Рэйнер Стропек](https://www.timecockpit.com) и [Дэниэл Рот](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7267d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="7267d-105">Публикация приложения</span><span class="sxs-lookup"><span data-stu-id="7267d-105">Publish the app</span></span>

<span data-ttu-id="7267d-106">Приложения публикуются для развертывания в конфигурации выпуска.</span><span class="sxs-lookup"><span data-stu-id="7267d-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7267d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7267d-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7267d-108">На панели навигации выберите **Сборка** > **Опубликовать {приложение}** .</span><span class="sxs-lookup"><span data-stu-id="7267d-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="7267d-109">Выберите *целевой объект публикации*.</span><span class="sxs-lookup"><span data-stu-id="7267d-109">Select the *publish target*.</span></span> <span data-ttu-id="7267d-110">Чтобы опубликовать объект в локальной среде, выберите **папку**.</span><span class="sxs-lookup"><span data-stu-id="7267d-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="7267d-111">Оставьте расположение по умолчанию в поле **выбора папки** или укажите другое расположение.</span><span class="sxs-lookup"><span data-stu-id="7267d-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="7267d-112">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="7267d-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7267d-113">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="7267d-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7267d-114">Щелкните **Сборка** > **Опубликовать в папке**.</span><span class="sxs-lookup"><span data-stu-id="7267d-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="7267d-115">Подтвердите, что папка должна получить опубликованные ресурсы, и щелкните **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="7267d-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7267d-116">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="7267d-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7267d-117">Используйте команду [dotnet publish](/dotnet/core/tools/dotnet-publish), чтобы опубликовать приложение с конфигурацией выпуска:</span><span class="sxs-lookup"><span data-stu-id="7267d-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="7267d-118">Публикация приложения активирует [восстановление](/dotnet/core/tools/dotnet-restore) зависимостей проекта и выполняет [сборку](/dotnet/core/tools/dotnet-build) проекта, прежде чем создавать ресурсы для развертывания.</span><span class="sxs-lookup"><span data-stu-id="7267d-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="7267d-119">В ходе процесса построения удаляются неиспользуемые методы и сборки, чтобы уменьшить размер скачиваемого приложения и время загрузки.</span><span class="sxs-lookup"><span data-stu-id="7267d-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="7267d-120">Расположения публикации.</span><span class="sxs-lookup"><span data-stu-id="7267d-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="7267d-121"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7267d-121"> WebAssembly</span></span>
  * <span data-ttu-id="7267d-122">Изолированное. Приложение публикуется в папку */bin/Release/{Целевая платформа}/publish/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7267d-122">Standalone: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="7267d-123">Чтобы развернуть приложение как статический сайт, скопируйте содержимое папки *wwwroot* на узел статического сайта.</span><span class="sxs-lookup"><span data-stu-id="7267d-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="7267d-124">Размещенное. Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{Целевая_платформа}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-124">Hosted: The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7267d-125">Разверните содержимое папки *publish* на узле.</span><span class="sxs-lookup"><span data-stu-id="7267d-125">Deploy the contents of the *publish* folder to the host.</span></span>
* <span data-ttu-id="7267d-126">Сервер Blazor. Приложение публикуется в папку */bin/Release/{Целевая платформа}/publish*.</span><span class="sxs-lookup"><span data-stu-id="7267d-126">Blazor Server: The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="7267d-127">Разверните содержимое папки *publish* на узле.</span><span class="sxs-lookup"><span data-stu-id="7267d-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="7267d-128">Ресурсы из папки развертываются на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="7267d-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="7267d-129">Развертывание может проводиться вручную или автоматизированно в зависимости от используемых средств разработки.</span><span class="sxs-lookup"><span data-stu-id="7267d-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="7267d-130">Базовый путь приложения</span><span class="sxs-lookup"><span data-stu-id="7267d-130">App base path</span></span>

<span data-ttu-id="7267d-131">*Базовый путь приложения*  — это корневой URL-путь приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="7267d-132">Рассмотрим следующее приложение ASP.NET Core и подприложение Blazor:</span><span class="sxs-lookup"><span data-stu-id="7267d-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="7267d-133">Приложение ASP.NET Core называется `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="7267d-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="7267d-134">Физическое расположение приложения: *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="7267d-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="7267d-135">запросы принимаются по адресу: `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="7267d-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="7267d-136">Приложение Blazor с именем `CoolApp` является подприложением `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="7267d-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="7267d-137">Физическое расположение подприложения — *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="7267d-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="7267d-138">запросы принимаются по адресу: `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="7267d-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="7267d-139">Если для `CoolApp` не указана дополнительная конфигурация, дочернее приложение в этом сценарии не имеет сведений о своем местоположении на сервере.</span><span class="sxs-lookup"><span data-stu-id="7267d-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="7267d-140">Например, приложение не может создавать правильные относительные URL-адреса к своим ресурсам, если ему неизвестно, что оно находится по относительному пути URL `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="7267d-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="7267d-141">Чтобы указать конфигурацию для базового пути к приложению Blazor`https://www.contoso.com/CoolApp/`, в качестве значения атрибута `href` тега `<base>` задается относительный корневой путь в файле *Pages/_Host.cshtml* (сервер Blazor) или в файле *wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="7267d-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="7267d-142">Серверные приложения Blazor дополнительно устанавливают базовый путь на стороне сервера путем вызова <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> в конвейере запросов приложения `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7267d-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="7267d-143">При указании относительного пути URL компонент, который не находится в корневом каталоге, может создавать URL-адреса относительно корневого пути приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="7267d-144">Компоненты, которые существуют на разных уровнях структуры каталогов, могут создавать ссылки на другие ресурсы во всех местах приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="7267d-145">Базовый путь к приложению также используется для перехвата выбранных гиперссылок, когда целевой объект ссылки `href` находится в пределах URI-пространства базового пути к приложению.</span><span class="sxs-lookup"><span data-stu-id="7267d-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="7267d-146">Маршрутизатор Blazor обрабатывает внутреннюю навигацию.</span><span class="sxs-lookup"><span data-stu-id="7267d-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="7267d-147">Во многих сценариях размещения относительный путь URL к приложению является корнем приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="7267d-148">В таких случаях относительный базовый путь URL к приложению представляет собой косую черту (`<base href="/" />`), что является конфигурацией по умолчанию для приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="7267d-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="7267d-149">В других сценариях размещения, таких как страницы GitHub и подприложения IIS, базовому пути приложения должно быть присвоено значение относительного пути URL к серверу для приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="7267d-150">Чтобы задать базовый путь к приложению, измените тег `<base>` в элементах `<head>` тега файла *Pages/_Host.cshtml* (сервер Blazor) или файла *wwwroot/index.html* (Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="7267d-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="7267d-151">Задайте значение атрибута `href` как `/{RELATIVE URL PATH}/` (косая черта в конце обязательна), где `{RELATIVE URL PATH}` — полный относительный путь URL приложения.</span><span class="sxs-lookup"><span data-stu-id="7267d-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="7267d-152">Приложение Blazor WebAssembly с некорневым относительным путем URL (например, `<base href="/CoolApp/">`) не сможет найти свои ресурсы *при локальном запуске*.</span><span class="sxs-lookup"><span data-stu-id="7267d-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="7267d-153">Для решения этой проблемы во время локальной разработки и тестирования можно предоставить аргумент *базового пути*, который соответствует значению `href` тега `<base>` во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="7267d-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="7267d-154">Не добавляйте в конце косую черту.</span><span class="sxs-lookup"><span data-stu-id="7267d-154">Don't include a trailing slash.</span></span> <span data-ttu-id="7267d-155">Для передачи аргумента базового пути при локальном запуске приложения выполните из каталога приложения команду `dotnet run` с параметром `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="7267d-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="7267d-156">Для приложения Blazor WebAssembly с относительным путем URL `/CoolApp/` (`<base href="/CoolApp/">`) команда имеет следующий вид:</span><span class="sxs-lookup"><span data-stu-id="7267d-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="7267d-157">Приложение Blazor WebAssembly отвечает локально по адресу `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="7267d-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="7267d-158">Развертывание</span><span class="sxs-lookup"><span data-stu-id="7267d-158">Deployment</span></span>

<span data-ttu-id="7267d-159">Инструкции по развертыванию см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="7267d-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
