---
title: Использование анализаторов веб-API
author: pranavkm
description: См. сведения о пакете анализаторов веб-API MVC ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 571046052dbe131e9cdcf981aaee0921ed8c2ea1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021852"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="c994c-103">Использование анализаторов веб-API</span><span class="sxs-lookup"><span data-stu-id="c994c-103">Use web API analyzers</span></span>

<span data-ttu-id="c994c-104">ASP.NET Core 2.2 и более поздних версий предоставляет пакет анализаторов MVC, предназначенный для использования с проектами веб-API.</span><span class="sxs-lookup"><span data-stu-id="c994c-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="c994c-105">Анализаторы работают с контроллерами, которые помечены с помощью <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>. Кроме того, к ним применяются [соглашения об использовании веб-API](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="c994c-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="c994c-106">Пакет анализаторов уведомляет вас о любом действии контроллера, которое:</span><span class="sxs-lookup"><span data-stu-id="c994c-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="c994c-107">возвращает необъявленный код состояния;</span><span class="sxs-lookup"><span data-stu-id="c994c-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="c994c-108">возвращает необъявленный успешный результат;</span><span class="sxs-lookup"><span data-stu-id="c994c-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="c994c-109">документирует код состояния, который не был получен;</span><span class="sxs-lookup"><span data-stu-id="c994c-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="c994c-110">включает проверку явной модели.</span><span class="sxs-lookup"><span data-stu-id="c994c-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="c994c-111">Создание ссылки на пакет анализатора</span><span class="sxs-lookup"><span data-stu-id="c994c-111">Reference the analyzer package</span></span>

<span data-ttu-id="c994c-112">В ASP.NET Core 3.0 или более поздней версии анализаторы включены в пакет SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c994c-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="c994c-113">Чтобы включить анализатор в проекте, включите свойство `IncludeOpenAPIAnalyzers` в файл проекта:</span><span class="sxs-lookup"><span data-stu-id="c994c-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="c994c-114">Установка пакета</span><span class="sxs-lookup"><span data-stu-id="c994c-114">Package installation</span></span>

<span data-ttu-id="c994c-115">Установите пакет [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet с помощью одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="c994c-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="c994c-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c994c-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c994c-117">В окне **Консоль диспетчера пакетов**</span><span class="sxs-lookup"><span data-stu-id="c994c-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="c994c-118">Перейдите к разделу **Просмотр** > **других** > **консолей диспетчера пакетов**Windows.</span><span class="sxs-lookup"><span data-stu-id="c994c-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="c994c-119">Перейдите в каталог, в котором находится файл *ApiConventions.csproj*</span><span class="sxs-lookup"><span data-stu-id="c994c-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="c994c-120">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c994c-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c994c-121">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="c994c-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c994c-122">Щелкните правой кнопкой мыши папку *пакеты* в **панель решения** > **Добавить пакеты..**..</span><span class="sxs-lookup"><span data-stu-id="c994c-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="c994c-123">Задайте в раскрывающемся списке " **Добавить пакеты** " **источник** "NuGet.org".</span><span class="sxs-lookup"><span data-stu-id="c994c-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="c994c-124">В поле поиска введите "Microsoft.AspNetCore.Mvc.Api.Analyzers".</span><span class="sxs-lookup"><span data-stu-id="c994c-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="c994c-125">В области результатов выберите пакет "Microsoft.AspNetCore.Mvc.Api.Analyzers", а затем нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="c994c-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="c994c-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c994c-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c994c-127">Во **встроенном терминале** выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="c994c-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="c994c-128">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="c994c-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c994c-129">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="c994c-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="c994c-130">Анализаторы для соглашений об использовании веб-API</span><span class="sxs-lookup"><span data-stu-id="c994c-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="c994c-131">Документы OpenAPI содержат коды состояний и типы ответов, которые может возвращать действие.</span><span class="sxs-lookup"><span data-stu-id="c994c-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="c994c-132">В ASP.NET Core MVC атрибуты, такие как <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> и <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>, используются для документирования действия.</span><span class="sxs-lookup"><span data-stu-id="c994c-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="c994c-133"><xref:tutorials/web-api-help-pages-using-swagger> позволяет более подробно документировать веб-API.</span><span class="sxs-lookup"><span data-stu-id="c994c-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="c994c-134">Один из анализаторов в пакете проверяет контроллеры, которые аннотированы <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, и определяет действия, которые не полностью документируют их ответы.</span><span class="sxs-lookup"><span data-stu-id="c994c-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="c994c-135">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="c994c-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="c994c-136">Предыдущее действие документирует тип возврата "Успех HTTP 200", но не документирует код состояния "Ошибка HTTP 404".</span><span class="sxs-lookup"><span data-stu-id="c994c-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="c994c-137">Анализатор сообщает об отсутствующем документировании для кода состояния HTTP 404 в виде предупреждения.</span><span class="sxs-lookup"><span data-stu-id="c994c-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="c994c-138">Эту проблему можно устранить.</span><span class="sxs-lookup"><span data-stu-id="c994c-138">An option to fix the problem is provided.</span></span>

![Предупреждение анализатора](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="c994c-140">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c994c-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
