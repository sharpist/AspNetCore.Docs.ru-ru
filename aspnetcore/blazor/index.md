---
title: 'Введение в ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Узнайте больше об использовании ASP.NET Core :::no-loc(Blazor)::: для создания интерактивного клиентского веб-интерфейса с помощью .NET в приложении ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/index
ms.openlocfilehash: ecdf4f59aca0fe71bbfcfe61a99109127c8b92df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055716"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="b1436-103">Введение в ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="b1436-103">Introduction to ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="b1436-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b1436-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b1436-105">*Добро пожаловать в :::no-loc(Blazor):::!*</span><span class="sxs-lookup"><span data-stu-id="b1436-105">*Welcome to :::no-loc(Blazor):::!*</span></span>

<span data-ttu-id="b1436-106">Платформа :::no-loc(Blazor)::: предназначена для создания интерактивного веб-интерфейса на стороне клиента с использованием [.NET](/dotnet/standard/tour) и предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="b1436-106">:::no-loc(Blazor)::: is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="b1436-107">создание многофункциональных интерактивных пользовательских интерфейсов на [C#](/dotnet/csharp/) вместо [JavaScript](https://www.javascript.com);</span><span class="sxs-lookup"><span data-stu-id="b1436-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="b1436-108">совместное использование серверной и клиентской логик приложений, написанных с помощью .NET;</span><span class="sxs-lookup"><span data-stu-id="b1436-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="b1436-109">отображение пользовательского интерфейса в виде HTML-страницы с CSS для широкой поддержки браузеров, в том числе для мобильных устройств.</span><span class="sxs-lookup"><span data-stu-id="b1436-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="b1436-110">интеграция с современными платформами размещения, такими как [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="b1436-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="b1436-111">Использование .NET для разработки веб-приложений на стороне клиента предоставляет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="b1436-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="b1436-112">создавайте код на C#, а не на JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b1436-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="b1436-113">возможность использовать существующую экосистему .NET с [библиотеками .NET](/dotnet/standard/class-libraries);</span><span class="sxs-lookup"><span data-stu-id="b1436-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="b1436-114">сохранение единой логики приложений для сервера и клиента;</span><span class="sxs-lookup"><span data-stu-id="b1436-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="b1436-115">высокая производительность, надежность и безопасность платформы .NET;</span><span class="sxs-lookup"><span data-stu-id="b1436-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="b1436-116">сохранение высокого уровня продуктивности с помощью [Visual Studio](https://visualstudio.microsoft.com) в Windows, Linux и macOS;</span><span class="sxs-lookup"><span data-stu-id="b1436-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="b1436-117">создавайте приложения на основе распространенных языков, платформ и инструментов, которые отличаются стабильностью, широким набором функций и простотой в использовании.</span><span class="sxs-lookup"><span data-stu-id="b1436-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="b1436-118">Компоненты</span><span class="sxs-lookup"><span data-stu-id="b1436-118">Components</span></span>

<span data-ttu-id="b1436-119">Приложения :::no-loc(Blazor)::: основаны на *компонентах* .</span><span class="sxs-lookup"><span data-stu-id="b1436-119">:::no-loc(Blazor)::: apps are based on *components* .</span></span> <span data-ttu-id="b1436-120">Компонентом :::no-loc(Blazor)::: называется любой элемент пользовательского интерфейса, например страница, диалоговое окно или форма ввода данных.</span><span class="sxs-lookup"><span data-stu-id="b1436-120">A component in :::no-loc(Blazor)::: is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="b1436-121">Компоненты являются классами C# .NET, встроенными в [сборки .NET](/dotnet/standard/assembly/), которые:</span><span class="sxs-lookup"><span data-stu-id="b1436-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="b1436-122">определяют гибкую логику визуализации пользовательского интерфейса;</span><span class="sxs-lookup"><span data-stu-id="b1436-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="b1436-123">обрабатывают действия пользователя;</span><span class="sxs-lookup"><span data-stu-id="b1436-123">Handle user events.</span></span>
* <span data-ttu-id="b1436-124">можно вкладывать друг в друга и использовать повторно;</span><span class="sxs-lookup"><span data-stu-id="b1436-124">Can be nested and reused.</span></span>
* <span data-ttu-id="b1436-125">можно совместно использовать и распространять в виде [библиотек классов :::no-loc(Razor):::](xref:razor-pages/ui-class) или [пакетов NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="b1436-125">Can be shared and distributed as [:::no-loc(Razor)::: class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="b1436-126">Класс компонента обычно записывается в виде страницы разметки [:::no-loc(Razor):::](xref:mvc/views/razor) с расширением файла `.razor`.</span><span class="sxs-lookup"><span data-stu-id="b1436-126">The component class is usually written in the form of a [:::no-loc(Razor):::](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="b1436-127">Обычно компоненты в :::no-loc(Blazor)::: называются *компонентами :::no-loc(Razor):::* .</span><span class="sxs-lookup"><span data-stu-id="b1436-127">Components in :::no-loc(Blazor)::: are formally referred to as *:::no-loc(Razor)::: components* .</span></span> <span data-ttu-id="b1436-128">Синтаксис :::no-loc(Razor)::: сочетает разметку HTML с кодом C#, позволяя повысить производительность разработчиков.</span><span class="sxs-lookup"><span data-stu-id="b1436-128">:::no-loc(Razor)::: is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="b1436-129">:::no-loc(Razor)::: позволяет переключаться между разметкой HTML и кодом C# в одном файле благодаря программной поддержке [IntelliSense](/visualstudio/ide/using-intellisense) в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b1436-129">:::no-loc(Razor)::: allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="b1436-130">Синтаксис :::no-loc(Razor)::: применяется также для :::no-loc(Razor)::: Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="b1436-130">:::no-loc(Razor)::: Pages and MVC also use :::no-loc(Razor):::.</span></span> <span data-ttu-id="b1436-131">В отличие от :::no-loc(Razor)::: Pages и MVC, которые построены по модели "запрос-ответ", компоненты используются специально для логики пользовательского интерфейса на стороне клиента и для компоновки.</span><span class="sxs-lookup"><span data-stu-id="b1436-131">Unlike :::no-loc(Razor)::: Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b1436-132">:::no-loc(Blazor)::: использует естественные теги HTML для компоновки пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b1436-132">:::no-loc(Blazor)::: uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="b1436-133">В следующем примере разметки :::no-loc(Razor)::: демонстрируется использование компонента (`Dialog.razor`), который отображает диалоговое окно и обрабатывает событие, когда пользователь нажимает кнопку:</span><span class="sxs-lookup"><span data-stu-id="b1436-133">The following :::no-loc(Razor)::: markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="b1436-134">В предыдущем примере метод C# `OnYes` инициируется событием кнопки `onclick`.</span><span class="sxs-lookup"><span data-stu-id="b1436-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="b1436-135">Текст (`ChildContent`) и заголовок (`Title`) диалогового окна предоставляются описанным ниже компонентом, который использует этот компонент в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="b1436-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="b1436-136">Компонент `Dialog` вложен в другой компонент с помощью HTML-тега.</span><span class="sxs-lookup"><span data-stu-id="b1436-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="b1436-137">В следующем примере компонент `Index` (`Pages/Index.razor`) использует предыдущий компонент `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="b1436-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="b1436-138">Атрибут `Title` тега передает значение заголовка в свойство `Title` компонента `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="b1436-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="b1436-139">Текст компонента `Dialog` (`ChildContent`) определяется содержимым элемента `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="b1436-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="b1436-140">Если компонент `Dialog` добавлен в компонент `Index`, [IntelliSense в Visual Studio](/visualstudio/ide/using-intellisense) позволяет ускорить разработку за счет дополнения элементов синтаксиса и параметров.</span><span class="sxs-lookup"><span data-stu-id="b1436-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about :::no-loc(Blazor):::?
</Dialog>
```

<span data-ttu-id="b1436-141">Когда к компоненту `Index` осуществляется доступ в браузере, отображается диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="b1436-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="b1436-142">Когда пользователь нажимает кнопку, в консоли средств разработчика браузера отображается сообщение, созданное методом `OnYes`:</span><span class="sxs-lookup"><span data-stu-id="b1436-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![В браузере отображается компонент Dialog как вложенный в компонент Index.](index/_static/dialog.png)

<span data-ttu-id="b1436-146">Компоненты преобразуются в хранящееся в памяти представление [модели DOM для браузера](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction), которое называется *деревом отображения* , позволяя гибко и эффективно обновлять пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="b1436-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree* , which is used to update the UI in a flexible and efficient way.</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="b1436-147">:::no-loc(Blazor WebAssembly)::: — это [платформа одностраничных приложений (SPA)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) для создания интерактивных клиентских веб-приложений с использованием .NET.</span><span class="sxs-lookup"><span data-stu-id="b1436-147">:::no-loc(Blazor WebAssembly)::: is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="b1436-148">:::no-loc(Blazor WebAssembly)::: использует открытые веб-стандарты без подключаемых модулей или перекомпиляции кода на другие языки.</span><span class="sxs-lookup"><span data-stu-id="b1436-148">:::no-loc(Blazor WebAssembly)::: uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="b1436-149">:::no-loc(Blazor WebAssembly)::: работает во всех современных веб-браузерах, включая браузеры мобильных устройств.</span><span class="sxs-lookup"><span data-stu-id="b1436-149">:::no-loc(Blazor WebAssembly)::: works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b1436-150">Выполнение кода .NET в веб-браузерах становится возможным благодаря технологии [WebAssembly](https://webassembly.org) (сокращенно `wasm`).</span><span class="sxs-lookup"><span data-stu-id="b1436-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="b1436-151">WebAssembly — это компактный формат байт-кода, оптимизированный для быстрой загрузки и максимального быстродействия.</span><span class="sxs-lookup"><span data-stu-id="b1436-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="b1436-152">WebAssembly — это открытый веб-стандарт, который поддерживается в веб-браузерах без подключаемых модулей.</span><span class="sxs-lookup"><span data-stu-id="b1436-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="b1436-153">Код WebAssembly может обращаться к любым функциям браузера через JavaScript благодаря поддержке *взаимодействия с JavaScript* (сокращенно *JavaScript interop* или *JS interop* ).</span><span class="sxs-lookup"><span data-stu-id="b1436-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* , often shortened to *JavaScript interop* or *JS interop* .</span></span> <span data-ttu-id="b1436-154">Код .NET, который обрабатывается через WebAssembly в браузере, выполняется в песочнице для JavaScript этого браузера, которая включает средства защиты от вредоносных действий на клиентском компьютере.</span><span class="sxs-lookup"><span data-stu-id="b1436-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![:::no-loc(Blazor WebAssembly)::: выполняет код .NET в браузере с WebAssembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="b1436-156">Когда приложение :::no-loc(Blazor WebAssembly)::: создано и запущено в браузере:</span><span class="sxs-lookup"><span data-stu-id="b1436-156">When a :::no-loc(Blazor WebAssembly)::: app is built and run in a browser:</span></span>

* <span data-ttu-id="b1436-157">Файлы кода C# и файлы :::no-loc(Razor)::: компилируются в сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="b1436-157">C# code files and :::no-loc(Razor)::: files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b1436-158">Сборки и [среда выполнения .NET](/dotnet/framework/get-started/overview) загружаются в браузер.</span><span class="sxs-lookup"><span data-stu-id="b1436-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="b1436-159">:::no-loc(Blazor WebAssembly)::: осуществляет начальную загрузку среды выполнения .NET и настраивает ее для загрузки сборок для приложения.</span><span class="sxs-lookup"><span data-stu-id="b1436-159">:::no-loc(Blazor WebAssembly)::: bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="b1436-160">Среда выполнения :::no-loc(Blazor WebAssembly)::: использует взаимодействие с JavaScript для обработки операций с моделью DOM и вызовов API браузера.</span><span class="sxs-lookup"><span data-stu-id="b1436-160">The :::no-loc(Blazor WebAssembly)::: runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="b1436-161">Размер опубликованного приложения, известный как *размер полезных данных* , является критически важным фактором производительности для удобства работы с приложением.</span><span class="sxs-lookup"><span data-stu-id="b1436-161">The size of the published app, its *payload size* , is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="b1436-162">Крупное приложение скачивается в браузере довольно долго, что ухудшает взаимодействие с пользователем.</span><span class="sxs-lookup"><span data-stu-id="b1436-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="b1436-163">:::no-loc(Blazor WebAssembly)::: оптимизирует размер полезных данных, чтобы сократить время скачивания:</span><span class="sxs-lookup"><span data-stu-id="b1436-163">:::no-loc(Blazor WebAssembly)::: optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="b1436-164">При публикации неиспользуемый код исключается из приложения [средством обрезки для промежуточного языка](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="b1436-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="b1436-165">HTTP-ответы сжимаются;</span><span class="sxs-lookup"><span data-stu-id="b1436-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b1436-166">среда выполнения и сборки .NET кэшируются в браузере.</span><span class="sxs-lookup"><span data-stu-id="b1436-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="b1436-167">При публикации неиспользуемый код исключается из приложения [компоновщиком промежуточного языка](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="b1436-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="b1436-168">HTTP-ответы сжимаются;</span><span class="sxs-lookup"><span data-stu-id="b1436-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b1436-169">среда выполнения и сборки .NET кэшируются в браузере.</span><span class="sxs-lookup"><span data-stu-id="b1436-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## :::no-loc(Blazor Server):::

<span data-ttu-id="b1436-170">:::no-loc(Blazor)::: отделяет логику отображения компонентов от того, как применяются обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b1436-170">:::no-loc(Blazor)::: decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="b1436-171">*:::no-loc(Blazor Server):::* поддерживает размещение компонентов :::no-loc(Razor)::: на сервере в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1436-171">*:::no-loc(Blazor Server):::* provides support for hosting :::no-loc(Razor)::: components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b1436-172">Обновления пользовательского интерфейса передаются через подключение [:::no-loc(SignalR):::](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="b1436-172">UI updates are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b1436-173">Среда выполнения обрабатывает следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b1436-173">The runtime handles:</span></span>

* <span data-ttu-id="b1436-174">отправка событий пользовательского интерфейса из браузера на сервер;</span><span class="sxs-lookup"><span data-stu-id="b1436-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="b1436-175">применение полученных с сервера обновлений пользовательского интерфейса к отображаемому компоненту.</span><span class="sxs-lookup"><span data-stu-id="b1436-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="b1436-176">Подключение, используемое :::no-loc(Blazor Server)::: для обмена данными с браузером, также применяется для обработки вызовов взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b1436-176">The connection used by :::no-loc(Blazor Server)::: to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![:::no-loc(Blazor Server)::: выполняет код .NET на сервере и взаимодействует с моделью DOM на стороне клиента через подключение :::no-loc(SignalR):::](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="b1436-178">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="b1436-178">JavaScript interop</span></span>

<span data-ttu-id="b1436-179">Для приложений, которым требуются сторонние библиотеки JavaScript и доступ к API браузера, компоненты взаимодействуют с JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b1436-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="b1436-180">Компоненты могут использовать любую библиотеку или API, которые может использовать JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b1436-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b1436-181">Код C# может [вызывать код JavaScript](xref:blazor/call-javascript-from-dotnet), а код JavaScript — [код C#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="b1436-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="b1436-182">Совместное использование кода и .NET Standard</span><span class="sxs-lookup"><span data-stu-id="b1436-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="b1436-183">:::no-loc(Blazor)::: реализует [.NET Standard](/dotnet/standard/net-standard), что позволяет проектам :::no-loc(Blazor)::: ссылаться на библиотеки, которые соответствуют спецификациям .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="b1436-183">:::no-loc(Blazor)::: implements the [.NET Standard](/dotnet/standard/net-standard), which enables :::no-loc(Blazor)::: projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="b1436-184">.NET Standard — это формальная спецификация API-интерфейсов .NET, которые доступны во всех реализациях .NET.</span><span class="sxs-lookup"><span data-stu-id="b1436-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="b1436-185">Библиотеки классов .NET Standard можно использовать на разных платформах .NET, таких как :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono и Unity.</span><span class="sxs-lookup"><span data-stu-id="b1436-185">.NET Standard class libraries can be shared across different .NET platforms, such as :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="b1436-186">API, которые не используются в веб-браузере (например, для доступа к файловой системе, открытия сокетов и работы с потоками), создают исключение <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="b1436-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1436-187">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b1436-187">Additional resources</span></span>

* [<span data-ttu-id="b1436-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b1436-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="b1436-189">Руководство по языку C#</span><span class="sxs-lookup"><span data-stu-id="b1436-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="b1436-190">HTML</span><span class="sxs-lookup"><span data-stu-id="b1436-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="b1436-191">Ссылки на сообщество [Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor)</span><span class="sxs-lookup"><span data-stu-id="b1436-191">[Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
