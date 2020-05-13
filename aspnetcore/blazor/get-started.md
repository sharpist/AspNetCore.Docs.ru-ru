---
title: Начало работы с Blazor ASP.NET Core
author: guardrex
description: Приступите к работе с Blazor, создав приложение Blazor с помощью предпочтительных средств.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769459"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="b0833-103">Начало работы с MVC ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b0833-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b0833-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0833-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b0833-105">Чтобы приступить к работе с Blazor, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="b0833-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0833-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0833-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0833-107">Чтобы создать приложения Blazor Server, установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="b0833-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b0833-108">Чтобы создавать приложения Blazor Server и Blazor WebAssembly, установите последнюю предварительную версию [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="b0833-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b0833-109">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b0833-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b0833-110">Установите шаблон предварительной версии Blazor WebAssembly, выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="b0833-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="b0833-111">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="b0833-111">Create a new project.</span></span>

1. <span data-ttu-id="b0833-112">Выберите **Приложение Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b0833-112">Select **Blazor App**.</span></span> <span data-ttu-id="b0833-113">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b0833-113">Select **Next**.</span></span>

1. <span data-ttu-id="b0833-114">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b0833-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b0833-115">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="b0833-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b0833-116">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b0833-116">Select **Create**.</span></span>

1. <span data-ttu-id="b0833-117">Для работы с Blazor WebAssembly (предварительная версия 2 Visual Studio 16.6 или более поздней версии) выберите шаблон **Приложение WebAssembly Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b0833-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b0833-118">Для работы с Blazor Server (Visual Studio 16.4 или более поздней версии) выберите шаблон **Серверное приложение Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b0833-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b0833-119">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b0833-119">Select **Create**.</span></span>

1. <span data-ttu-id="b0833-120">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b0833-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0833-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0833-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b0833-122">Установите [пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b0833-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b0833-123">При необходимости установите шаблон предварительной версии [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), выполнив следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b0833-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="b0833-124">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b0833-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b0833-125">[Пакет SDK для .NET Core версии 3.1.201 или более поздней](https://dotnet.microsoft.com/download/dotnet-core/3.1) **обязательно** должен использовать шаблон предварительной версии Blazor WebAssembly 3.2.</span><span class="sxs-lookup"><span data-stu-id="b0833-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="b0833-126">Подтвердите установленную версию пакет SDK для .NET Core, запустив `dotnet --version` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="b0833-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b0833-127">Установите [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b0833-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="b0833-128">Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="b0833-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="b0833-129">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b0833-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b0833-130">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b0833-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="b0833-131">Откройте папку *WebApplication1* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b0833-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b0833-132">Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта.</span><span class="sxs-lookup"><span data-stu-id="b0833-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b0833-133">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="b0833-133">Select **Yes**.</span></span>

1. <span data-ttu-id="b0833-134">При использовании Blazor Server запустите приложение с помощью отладчика Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b0833-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="b0833-135">С помощью Blazor WebAssembly запустите конфигурацию **.NET Core Launch (Blazor Standalone**), а затем запустите браузер в конфигурации запуска **.NET Core Debug Blazor Web Assembly в Chrome** (эта конфигурация требует наличия Chrome).</span><span class="sxs-lookup"><span data-stu-id="b0833-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b0833-136">Для получения дополнительной информации см. <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="b0833-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="b0833-137">В браузере перейдите на адрес `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b0833-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b0833-138">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b0833-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b0833-139">Blazor Server поддерживается в Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="b0833-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="b0833-140">Blazor WebAssembly сейчас не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b0833-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="b0833-141">Чтобы создавать приложения Blazor WebAssembly в macOS, следуйте указаниям на вкладке **.NET Core CLI**. Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b0833-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b0833-142">Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b0833-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b0833-143">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="b0833-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b0833-144">На боковой панели выберите **.NET Core** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="b0833-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="b0833-145">Выберите шаблон **Серверное приложение Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b0833-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="b0833-146">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b0833-146">Select **Next**.</span></span>

1. <span data-ttu-id="b0833-147">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="b0833-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="b0833-148">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="b0833-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="b0833-149">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="b0833-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="b0833-150">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b0833-150">Select **Next**.</span></span>

1. <span data-ttu-id="b0833-151">В поле **Имя проекта** присвойте имя приложению `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="b0833-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b0833-152">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b0833-152">Select **Create**.</span></span>

1. <span data-ttu-id="b0833-153">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*.</span><span class="sxs-lookup"><span data-stu-id="b0833-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b0833-154">В настоящее время отладка не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="b0833-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="b0833-155">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="b0833-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b0833-156">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="b0833-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b0833-157">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="b0833-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b0833-158">Установите [пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b0833-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b0833-159">При необходимости установите шаблон предварительной версии Blazor WebAssembly, выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="b0833-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="b0833-160">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b0833-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b0833-161">[Пакет SDK для .NET Core версии 3.1.201 или более поздней](https://dotnet.microsoft.com/download/dotnet-core/3.1) **обязательно** должен использовать шаблон предварительной версии Blazor WebAssembly 3.2.</span><span class="sxs-lookup"><span data-stu-id="b0833-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="b0833-162">Подтвердите установленную версию пакет SDK для .NET Core, запустив `dotnet --version` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="b0833-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b0833-163">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b0833-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b0833-164">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b0833-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="b0833-165">В браузере перейдите на адрес `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b0833-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="b0833-166">На вкладках на боковой панели доступно несколько страниц:</span><span class="sxs-lookup"><span data-stu-id="b0833-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b0833-167">Главная страница</span><span class="sxs-lookup"><span data-stu-id="b0833-167">Home</span></span>
* <span data-ttu-id="b0833-168">Счетчик</span><span class="sxs-lookup"><span data-stu-id="b0833-168">Counter</span></span>
* <span data-ttu-id="b0833-169">Выборка данных</span><span class="sxs-lookup"><span data-stu-id="b0833-169">Fetch data</span></span>

<span data-ttu-id="b0833-170">На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы.</span><span class="sxs-lookup"><span data-stu-id="b0833-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b0833-171">Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript, однако с Blazor можно использовать C#.</span><span class="sxs-lookup"><span data-stu-id="b0833-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b0833-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b0833-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b0833-173">Запрос `/counter` в браузере, как указано в директиве `@page` в верхней части, заставляет компонент `Counter` визуализировать свое содержимое.</span><span class="sxs-lookup"><span data-stu-id="b0833-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b0833-174">Компоненты преобразуются в представление дерева визуализации. Его затем можно использовать для гибкого и эффективного обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b0833-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b0833-175">Каждый раз при нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b0833-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="b0833-176">Запускается событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="b0833-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b0833-177">вызывается метод `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="b0833-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b0833-178">Увеличивается значение `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b0833-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b0833-179">Компонент визуализируется снова.</span><span class="sxs-lookup"><span data-stu-id="b0833-179">The component is rendered again.</span></span>

<span data-ttu-id="b0833-180">Среда выполнения сравнивает новое содержимое с предыдущим содержимым и применяет к модели DOM только измененное содержимое.</span><span class="sxs-lookup"><span data-stu-id="b0833-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b0833-181">Добавьте компонент в другой компонент, используя синтаксис HTML.</span><span class="sxs-lookup"><span data-stu-id="b0833-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b0833-182">Например, добавьте компонент `Counter` на домашнюю страницу приложения, разместив элемент `<Counter />` внутри компонента `Index`.</span><span class="sxs-lookup"><span data-stu-id="b0833-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b0833-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b0833-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b0833-184">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b0833-184">Run the app.</span></span> <span data-ttu-id="b0833-185">На домашней странице имеется собственный счетчик, предоставленный компонентом `Counter`.</span><span class="sxs-lookup"><span data-stu-id="b0833-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b0833-186">Параметры компонента указываются с помощью атрибутов или [дочернего содержимого](xref:blazor/components#child-content), которые позволяют задавать свойства дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="b0833-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b0833-187">Чтобы добавить параметр в компонент `Counter`, обновите блок `@code`компонента:</span><span class="sxs-lookup"><span data-stu-id="b0833-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b0833-188">Добавьте открытое свойство для `IncrementAmount` с атрибутом `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b0833-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="b0833-189">Изменение метод `IncrementCount`, чтобы он использовал `IncrementAmount` при увеличении значения `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b0833-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b0833-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b0833-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b0833-191">Укажите `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.</span><span class="sxs-lookup"><span data-stu-id="b0833-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b0833-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b0833-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b0833-193">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b0833-193">Run the app.</span></span> <span data-ttu-id="b0833-194">Теперь у компонента `Index` будет собственный счетчик, который будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь).</span><span class="sxs-lookup"><span data-stu-id="b0833-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b0833-195">Компонент `Counter` (*Counter.razor*) в компоненте `/counter` продолжает увеличиваться на единицу.</span><span class="sxs-lookup"><span data-stu-id="b0833-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0833-196">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="b0833-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b0833-197">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b0833-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
