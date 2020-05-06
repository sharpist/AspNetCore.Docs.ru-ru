---
title: Введение в ASP.NET Core Blazor
author: guardrex
description: Узнайте больше об использовании ASP.NET Core Blazor для создания интерактивного клиентского веб-интерфейса с помощью .NET в приложении ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: ced3e2cc0428fccf6f0b2eba7a3f045e07002234
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771954"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="d3663-103">Введение в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d3663-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="d3663-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d3663-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d3663-105">*Добро пожаловать в Blazor!*</span><span class="sxs-lookup"><span data-stu-id="d3663-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="d3663-106">Платформа Blazor предназначена для создания интерактивного веб-интерфейса на стороне клиента для .NET и предлагает следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="d3663-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="d3663-107">создание многофункциональных интерактивных пользовательских интерфейсов на C# вместо JavaScript;</span><span class="sxs-lookup"><span data-stu-id="d3663-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="d3663-108">совместное использование серверной и клиентской логик приложений, написанных с помощью .NET;</span><span class="sxs-lookup"><span data-stu-id="d3663-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="d3663-109">отображение пользовательского интерфейса в виде HTML-страницы с CSS для широкой поддержки браузеров, в том числе для мобильных устройств.</span><span class="sxs-lookup"><span data-stu-id="d3663-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="d3663-110">интеграция с современными платформами размещения, такими как [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="d3663-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="d3663-111">Использование .NET для разработки веб-приложений на стороне клиента предоставляет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="d3663-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="d3663-112">создавайте код на C#, а не на JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d3663-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="d3663-113">возможность использования существующей экосистемы .NET с библиотеками .NET;</span><span class="sxs-lookup"><span data-stu-id="d3663-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="d3663-114">сохранение единой логики приложений для сервера и клиента;</span><span class="sxs-lookup"><span data-stu-id="d3663-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="d3663-115">высокая производительность, надежность и безопасность платформы .NET;</span><span class="sxs-lookup"><span data-stu-id="d3663-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="d3663-116">сохраняйте высокий уровень продуктивности с помощью Visual Studio в Windows, Linux и macOS.</span><span class="sxs-lookup"><span data-stu-id="d3663-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="d3663-117">создавайте приложения на основе распространенных языков, платформ и инструментов, которые отличаются стабильностью, широким набором функций и простотой в использовании.</span><span class="sxs-lookup"><span data-stu-id="d3663-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="d3663-118">Компоненты</span><span class="sxs-lookup"><span data-stu-id="d3663-118">Components</span></span>

<span data-ttu-id="d3663-119">Приложения Blazor основаны на *компонентах*.</span><span class="sxs-lookup"><span data-stu-id="d3663-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="d3663-120">Компонентом Blazor называется любой элемент пользовательского интерфейса, например страница, диалоговое окно или форма ввода данных.</span><span class="sxs-lookup"><span data-stu-id="d3663-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="d3663-121">Компоненты являются классами .NET, встроенными в сборки .NET, которые:</span><span class="sxs-lookup"><span data-stu-id="d3663-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="d3663-122">определяют гибкую логику визуализации пользовательского интерфейса;</span><span class="sxs-lookup"><span data-stu-id="d3663-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="d3663-123">обрабатывают действия пользователя;</span><span class="sxs-lookup"><span data-stu-id="d3663-123">Handle user events.</span></span>
* <span data-ttu-id="d3663-124">можно вкладывать друг в друга и использовать повторно;</span><span class="sxs-lookup"><span data-stu-id="d3663-124">Can be nested and reused.</span></span>
* <span data-ttu-id="d3663-125">можно совместно использовать и распространять в виде [библиотек классов Razor](xref:razor-pages/ui-class) или [пакетов NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="d3663-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="d3663-126">Класс компонента обычно записывается в виде страницы разметки [Razor](xref:mvc/views/razor) с расширением файла *.razor*.</span><span class="sxs-lookup"><span data-stu-id="d3663-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="d3663-127">Обычно компоненты в Blazor называются *компонентами Razor* .</span><span class="sxs-lookup"><span data-stu-id="d3663-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="d3663-128">Синтаксис Razor сочетает разметку HTML с кодом C#, позволяя повысить производительность разработчиков.</span><span class="sxs-lookup"><span data-stu-id="d3663-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="d3663-129"> позволяет переключаться между разметкой HTML и кодом C# в одном файле с поддержкой [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="d3663-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="d3663-130">Синтаксис Razor применяется также для Razor Pages и MVC.</span><span class="sxs-lookup"><span data-stu-id="d3663-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="d3663-131">В отличие от Razor Pages и MVC, которые построены по модели "запрос-ответ", компоненты используются специально для логики пользовательского интерфейса на стороне клиента и для компоновки.</span><span class="sxs-lookup"><span data-stu-id="d3663-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="d3663-132">Следующая разметка Razor определяет компонент *Dialog.razor*, который может быть вложен в другой компонент:</span><span class="sxs-lookup"><span data-stu-id="d3663-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="d3663-133">Содержимое текста (`ChildContent`) и заголовка (`Title`) диалогового окна предоставляются компонентом, который использует этот компонент в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="d3663-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="d3663-134">Метод C# `OnYes` инициируется событием кнопки `onclick`.</span><span class="sxs-lookup"><span data-stu-id="d3663-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="d3663-135"> использует естественные теги HTML для компоновки пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="d3663-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="d3663-136">Элементы HTML определяют компоненты, а атрибуты тегов передают значения для свойств компонентов.</span><span class="sxs-lookup"><span data-stu-id="d3663-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="d3663-137">В следующем примере компонент `Index` использует компонент `Dialog`.</span><span class="sxs-lookup"><span data-stu-id="d3663-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="d3663-138">Значения `ChildContent` и `Title` определяются атрибутами и содержимым элемента `<Dialog>`.</span><span class="sxs-lookup"><span data-stu-id="d3663-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="d3663-139">*Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d3663-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="d3663-140">Диалоговое окно отображается, когда к родительскому компоненту (*Index.razor*) осуществляется доступ в браузере:</span><span class="sxs-lookup"><span data-stu-id="d3663-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Компонент диалогового окна, отображаемый в браузере](index/_static/dialog.png)

<span data-ttu-id="d3663-142">Если этот компонент используется в приложении, технология IntelliSense в [Visual Studio](/visualstudio/ide/using-intellisense) и [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) ускоряет разработку благодаря завершению синтаксиса и параметров.</span><span class="sxs-lookup"><span data-stu-id="d3663-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="d3663-143">Компоненты преобразуются в хранящееся в памяти представление модели DOM для браузера, которое называется *деревом отображения*, позволяя гибко и эффективно обновлять пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="d3663-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="d3663-144"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d3663-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="d3663-145"> WebAssembly — это платформа для одностраничных приложений, которая позволяет создавать интерактивные клиентские веб-приложения с помощью .NET.</span><span class="sxs-lookup"><span data-stu-id="d3663-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="d3663-146"> WebAssembly использует открытые веб-стандарты без подключаемых модулей или транскомпиляции кода. Эта платформа работает во всех современных веб-браузерах, в том числе на мобильных устройствах.</span><span class="sxs-lookup"><span data-stu-id="d3663-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="d3663-147">Выполнение кода .NET в веб-браузерах становится возможным благодаря технологии [WebAssembly](https://webassembly.org) (сокращенно *wasm*).</span><span class="sxs-lookup"><span data-stu-id="d3663-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="d3663-148">WebAssembly — это компактный формат байт-кода, оптимизированный для быстрой загрузки и максимального быстродействия.</span><span class="sxs-lookup"><span data-stu-id="d3663-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="d3663-149">WebAssembly — это открытый веб-стандарт, который поддерживается в веб-браузерах без подключаемых модулей.</span><span class="sxs-lookup"><span data-stu-id="d3663-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="d3663-150">Код WebAssembly может обращаться к любым функциям браузера через JavaScript благодаря поддержке *взаимодействия с JavaScript* *.*</span><span class="sxs-lookup"><span data-stu-id="d3663-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="d3663-151">Код .NET, который обрабатывается через WebAssembly в браузере, выполняется в песочнице для JavaScript этого браузера, которая включает средства защиты от вредоносных действий на клиентском компьютере.</span><span class="sxs-lookup"><span data-stu-id="d3663-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="d3663-152">![Blazor WebAssembly выполняет код .NET в браузере с WebAssembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="d3663-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="d3663-153">Когда приложение Blazor WebAssembly создается и запускается в браузере:</span><span class="sxs-lookup"><span data-stu-id="d3663-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="d3663-154">Файлы кода C# и файлы Razor компилируются в сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="d3663-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="d3663-155">Сборки и среда выполнения .NET загружаются в браузер.</span><span class="sxs-lookup"><span data-stu-id="d3663-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="d3663-156"> WebAssembly осуществляет начальную загрузку среды выполнения .NET и настраивает ее для загрузки сборок для приложения.</span><span class="sxs-lookup"><span data-stu-id="d3663-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="d3663-157">Среда выполнения Blazor WebAssembly использует взаимодействие с JavaScript для обработки операций с моделью DOM и вызовов API браузера.</span><span class="sxs-lookup"><span data-stu-id="d3663-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="d3663-158">Размер опубликованного приложения, известный как *размер полезных данных*, является критически важным фактором производительности для удобства работы с приложением.</span><span class="sxs-lookup"><span data-stu-id="d3663-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="d3663-159">Крупное приложение скачивается в браузере довольно долго, что ухудшает взаимодействие с пользователем.</span><span class="sxs-lookup"><span data-stu-id="d3663-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="d3663-160"> WebAssembly оптимизирует размер полезных данных, чтобы сократить время скачивания:</span><span class="sxs-lookup"><span data-stu-id="d3663-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="d3663-161">При публикации неиспользуемый код исключается из приложения [компоновщиком промежуточного языка](xref:host-and-deploy/blazor/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="d3663-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="d3663-162">HTTP-ответы сжимаются;</span><span class="sxs-lookup"><span data-stu-id="d3663-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="d3663-163">среда выполнения и сборки .NET кэшируются в браузере.</span><span class="sxs-lookup"><span data-stu-id="d3663-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="d3663-164"> Server</span><span class="sxs-lookup"><span data-stu-id="d3663-164"> Server</span></span>

Blazor<span data-ttu-id="d3663-165"> отделяет логику отображения компонентов от того, как применяются обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="d3663-165"> decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="d3663-166">Сервер Blazor предоставляет поддержку размещения компонентов Razor на сервере в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3663-166">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="d3663-167">Обновления пользовательского интерфейса передаются через подключение [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="d3663-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d3663-168">Среда выполнения обрабатывает отправку событий пользовательского интерфейса из браузера на сервер и применяет полученные от сервера обновления пользовательского интерфейса в браузере после выполнения компонентов.</span><span class="sxs-lookup"><span data-stu-id="d3663-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="d3663-169">Подключение, используемое Blazor Server для обмена данными с браузером, также применяется для обработки вызовов взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d3663-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="d3663-170">![Blazor Server выполняет код .NET на сервере и взаимодействует с моделью DOM на стороне клиента через подключение SignalR](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="d3663-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="d3663-171">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="d3663-171">JavaScript interop</span></span>

<span data-ttu-id="d3663-172">Для приложений, которым требуются сторонние библиотеки JavaScript и доступ к API браузера, компоненты взаимодействуют с JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d3663-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="d3663-173">Компоненты могут использовать любую библиотеку или API, которые может использовать JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d3663-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="d3663-174">Код C# может вызывать код JavaScript, а код JavaScript — код C#.</span><span class="sxs-lookup"><span data-stu-id="d3663-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="d3663-175">Дополнительные сведения см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="d3663-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="d3663-176">Совместное использование кода и .NET Standard</span><span class="sxs-lookup"><span data-stu-id="d3663-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="d3663-177"> реализует [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="d3663-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="d3663-178">.NET Standard — это формальная спецификация API-интерфейсов .NET, которые доступны во всех реализациях .NET.</span><span class="sxs-lookup"><span data-stu-id="d3663-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="d3663-179">Библиотеки классов .NET Standard можно использовать на разных платформах .NET, таких как Blazor, .NET Framework, .NET Core, Xamarin, Mono и Unity.</span><span class="sxs-lookup"><span data-stu-id="d3663-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="d3663-180">API, которые не используются в веб-браузере (например, для доступа к файловой системе, открытия сокетов и работы с потоками), создают исключение <xref:System.PlatformNotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="d3663-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3663-181">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d3663-181">Additional resources</span></span>

* [<span data-ttu-id="d3663-182">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d3663-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="d3663-183">Руководство по языку C#</span><span class="sxs-lookup"><span data-stu-id="d3663-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="d3663-184">HTML</span><span class="sxs-lookup"><span data-stu-id="d3663-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="d3663-185">Ссылки на сообщество [Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor)</span><span class="sxs-lookup"><span data-stu-id="d3663-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
