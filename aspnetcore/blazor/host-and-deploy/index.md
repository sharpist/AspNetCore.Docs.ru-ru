---
title: Размещение и развертывание ASP.NET Core Blazor
author: guardrex
description: Узнайте, как размещать и развертывать приложения Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: a23bee120611ee603305a88dabac76566481fa4a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485892"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="b9f32-103">Размещение и развертывание ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b9f32-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="b9f32-104">Авторы: [Люк Лэтем](https://github.com/guardrex), [Рэйнер Стропек](https://www.timecockpit.com) и [Дэниэл Рот](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b9f32-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b9f32-105">Публикация приложения</span><span class="sxs-lookup"><span data-stu-id="b9f32-105">Publish the app</span></span>

<span data-ttu-id="b9f32-106">Приложения публикуются для развертывания в конфигурации выпуска.</span><span class="sxs-lookup"><span data-stu-id="b9f32-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b9f32-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9f32-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b9f32-108">На панели навигации выберите **Сборка** > **Опубликовать {приложение}** .</span><span class="sxs-lookup"><span data-stu-id="b9f32-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="b9f32-109">Выберите *целевой объект публикации*.</span><span class="sxs-lookup"><span data-stu-id="b9f32-109">Select the *publish target*.</span></span> <span data-ttu-id="b9f32-110">Чтобы опубликовать объект в локальной среде, выберите **папку**.</span><span class="sxs-lookup"><span data-stu-id="b9f32-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="b9f32-111">Оставьте расположение по умолчанию в поле **выбора папки** или укажите другое расположение.</span><span class="sxs-lookup"><span data-stu-id="b9f32-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="b9f32-112">Нажмите кнопку **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="b9f32-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b9f32-113">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b9f32-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b9f32-114">Щелкните **Сборка** > **Опубликовать в папке**.</span><span class="sxs-lookup"><span data-stu-id="b9f32-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="b9f32-115">Подтвердите, что папка должна получить опубликованные ресурсы, и щелкните **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="b9f32-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b9f32-116">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="b9f32-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b9f32-117">Используйте команду [`dotnet publish`](/dotnet/core/tools/dotnet-publish), чтобы опубликовать приложение с конфигурацией выпуска.</span><span class="sxs-lookup"><span data-stu-id="b9f32-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="b9f32-118">Публикация приложения активирует [восстановление](/dotnet/core/tools/dotnet-restore) зависимостей проекта и выполняет [сборку](/dotnet/core/tools/dotnet-build) проекта, прежде чем создавать ресурсы для развертывания.</span><span class="sxs-lookup"><span data-stu-id="b9f32-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b9f32-119">В ходе процесса построения удаляются неиспользуемые методы и сборки, чтобы уменьшить размер скачиваемого приложения и время загрузки.</span><span class="sxs-lookup"><span data-stu-id="b9f32-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="b9f32-120">Расположения публикации.</span><span class="sxs-lookup"><span data-stu-id="b9f32-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="b9f32-121">Изолированное. Приложение будет опубликовано в папке `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="b9f32-122">Чтобы развернуть приложение как статический сайт, скопируйте содержимое папки `wwwroot` на узел статического сайта.</span><span class="sxs-lookup"><span data-stu-id="b9f32-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="b9f32-123">Размещенное. Клиентское приложение Blazor WebAssembly будет опубликовано в папку `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` серверного приложения вместе со статическими веб-ресурсами серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b9f32-124">Разверните содержимое папки `publish` на узле.</span><span class="sxs-lookup"><span data-stu-id="b9f32-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="b9f32-125">Blazor Server. Приложение будет опубликовано в папке `/bin/Release/{TARGET FRAMEWORK}/publish`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-125">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="b9f32-126">Разверните содержимое папки `publish` на узле.</span><span class="sxs-lookup"><span data-stu-id="b9f32-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="b9f32-127">Ресурсы из папки развертываются на веб-сервере.</span><span class="sxs-lookup"><span data-stu-id="b9f32-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="b9f32-128">Развертывание может проводиться вручную или автоматизированно в зависимости от используемых средств разработки.</span><span class="sxs-lookup"><span data-stu-id="b9f32-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="b9f32-129">Базовый путь приложения</span><span class="sxs-lookup"><span data-stu-id="b9f32-129">App base path</span></span>

<span data-ttu-id="b9f32-130">*Базовый путь приложения*  — это корневой URL-путь приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="b9f32-131">Рассмотрим следующее приложение ASP.NET Core и подприложение Blazor:</span><span class="sxs-lookup"><span data-stu-id="b9f32-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="b9f32-132">Приложение ASP.NET Core называется `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="b9f32-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="b9f32-133">Физическое расположение приложения: `d:/MyApp`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="b9f32-134">запросы принимаются по адресу: `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="b9f32-135">Приложение Blazor с именем `CoolApp` является подприложением `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="b9f32-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="b9f32-136">Физическое расположение дочернего приложения: `d:/MyApp/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="b9f32-137">запросы принимаются по адресу: `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="b9f32-138">Если для `CoolApp` не указана дополнительная конфигурация, дочернее приложение в этом сценарии не имеет сведений о своем местоположении на сервере.</span><span class="sxs-lookup"><span data-stu-id="b9f32-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="b9f32-139">Например, приложение не может создавать правильные относительные URL-адреса к своим ресурсам, если ему неизвестно, что оно находится по относительному пути URL `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="b9f32-140">Чтобы указать конфигурацию для базового пути к приложению Blazor `https://www.contoso.com/CoolApp/`, в качестве значения атрибута `href` тега `<base>` задается относительный корневой путь в файле `wwwroot/index.html` (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="b9f32-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="b9f32-141">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="b9f32-141">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="b9f32-142">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="b9f32-142">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="b9f32-143">Приложения Blazor Server дополнительно устанавливают базовый путь на стороне сервера путем вызова <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> в конвейере запросов приложения `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b9f32-143">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="b9f32-144">При указании относительного пути URL компонент, который не находится в корневом каталоге, может создавать URL-адреса относительно корневого пути приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-144">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="b9f32-145">Компоненты, которые существуют на разных уровнях структуры каталогов, могут создавать ссылки на другие ресурсы во всех местах приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-145">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="b9f32-146">Базовый путь к приложению также используется для перехвата выбранных гиперссылок, когда целевой объект ссылки `href` находится в пределах URI-пространства базового пути к приложению.</span><span class="sxs-lookup"><span data-stu-id="b9f32-146">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="b9f32-147">Маршрутизатор Blazor обрабатывает внутреннюю навигацию.</span><span class="sxs-lookup"><span data-stu-id="b9f32-147">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="b9f32-148">Во многих сценариях размещения относительный путь URL к приложению является корнем приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-148">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="b9f32-149">В таких случаях относительный базовый путь URL к приложению представляет собой косую черту (`<base href="/" />`), что является конфигурацией по умолчанию для приложения Blazor.</span><span class="sxs-lookup"><span data-stu-id="b9f32-149">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="b9f32-150">В других сценариях размещения, таких как страницы GitHub и подприложения IIS, базовому пути приложения должно быть присвоено значение относительного пути URL к серверу для приложения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-150">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="b9f32-151">Чтобы задать базовый путь к приложению, измените тег `<base>` в элементах `<head>` тега файла `Pages/_Host.cshtml` (Blazor Server) или файла `wwwroot/index.html` (Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="b9f32-151">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="b9f32-152">Задайте для атрибута `href` значение `/{RELATIVE URL PATH}/` (Blazor WebAssembly) или `~/{RELATIVE URL PATH}/` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="b9f32-152">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="b9f32-153">**Требуется завершающая косая черта.**</span><span class="sxs-lookup"><span data-stu-id="b9f32-153">**The trailing slash is required.**</span></span> <span data-ttu-id="b9f32-154">Заполнитель `{RELATIVE URL PATH}` — это полный относительный URL-путь к приложению.</span><span class="sxs-lookup"><span data-stu-id="b9f32-154">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="b9f32-155">Приложение Blazor WebAssembly с некорневым относительным путем URL (например, `<base href="/CoolApp/">`) не сможет найти свои ресурсы при *локальном запуске*.</span><span class="sxs-lookup"><span data-stu-id="b9f32-155">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="b9f32-156">Для решения этой проблемы во время локальной разработки и тестирования можно предоставить аргумент *базового пути*, который соответствует значению `href` тега `<base>` во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="b9f32-156">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="b9f32-157">**Не добавляйте в конце косую черту.**</span><span class="sxs-lookup"><span data-stu-id="b9f32-157">**Don't include a trailing slash.**</span></span> <span data-ttu-id="b9f32-158">Для передачи аргумента базового пути при локальном запуске приложения выполните из каталога приложения команду `dotnet run` с параметром `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="b9f32-158">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="b9f32-159">Для приложения Blazor WebAssembly с относительным путем URL `/CoolApp/` (`<base href="/CoolApp/">`) команда имеет следующий вид:</span><span class="sxs-lookup"><span data-stu-id="b9f32-159">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="b9f32-160">Приложение Blazor WebAssembly отвечает локально по `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="b9f32-160">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="b9f32-161">**Конфигурация Blazor Server `MapFallbackToPage`**</span><span class="sxs-lookup"><span data-stu-id="b9f32-161">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="b9f32-162">Передайте следующий путь к <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b9f32-162">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="b9f32-163">Заполнитель `{RELATIVE PATH}` используется для пути к некорневому каталогу на сервере.</span><span class="sxs-lookup"><span data-stu-id="b9f32-163">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="b9f32-164">Например, `CoolApp` будет сегментом заполнителя, если URL-адрес некорневого каталога приложения — `https://{HOST}:{PORT}/CoolApp/`):</span><span class="sxs-lookup"><span data-stu-id="b9f32-164">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="b9f32-165">**Размещение нескольких приложений Blazor WebAssembly**</span><span class="sxs-lookup"><span data-stu-id="b9f32-165">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="b9f32-166">Дополнительные сведения о размещении нескольких приложений Blazor WebAssembly в размещенном решении Blazor см. в разделе <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span><span class="sxs-lookup"><span data-stu-id="b9f32-166">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="b9f32-167">Развертывание</span><span class="sxs-lookup"><span data-stu-id="b9f32-167">Deployment</span></span>

<span data-ttu-id="b9f32-168">Инструкции по развертыванию см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="b9f32-168">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
