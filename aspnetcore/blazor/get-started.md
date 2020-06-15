---
title: Начало работы с Blazor ASP.NET Core
author: guardrex
description: Приступите к работе с Blazor, создав приложение Blazor с помощью предпочтительных средств.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452035"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="cf4cf-103">Начало работы с Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf4cf-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="cf4cf-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cf4cf-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cf4cf-105">Чтобы приступить к работе с Blazor, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cf4cf-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cf4cf-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cf4cf-107">Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="cf4cf-108">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-108">Create a new project.</span></span>

1. <span data-ttu-id="cf4cf-109">Выберите **Приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="cf4cf-109">Select **Blazor App**.</span></span> <span data-ttu-id="cf4cf-110">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-110">Select **Next**.</span></span>

1. <span data-ttu-id="cf4cf-111">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cf4cf-112">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="cf4cf-113">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-113">Select **Create**.</span></span>

1. <span data-ttu-id="cf4cf-114">Для работы с Blazor WebAssembly выберите шаблон **Приложение Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="cf4cf-115">Для работы с Blazor Server выберите шаблон **Серверное приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="cf4cf-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="cf4cf-116">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-116">Select **Create**.</span></span>

   <span data-ttu-id="cf4cf-117">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cf4cf-118">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cf4cf-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cf4cf-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cf4cf-120">Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="cf4cf-121">Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="cf4cf-122">Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="cf4cf-123">Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="cf4cf-124">Чтобы задать `debug.javascript.usePreview` для `true` с помощью пользовательского интерфейса VS Code, откройте **Файл** > **Настройки** > **Параметры** и выполните поиск `debug javascript use preview`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="cf4cf-125">Установите флажок **Использовать новый отладчик JavaScript в предварительной версии для Node.js и Chrome**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="cf4cf-126">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="cf4cf-127">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="cf4cf-128">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cf4cf-129">Откройте папку *WebApplication1* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="cf4cf-130">Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="cf4cf-131">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-131">Select **Yes**.</span></span>

1. <span data-ttu-id="cf4cf-132">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cf4cf-133">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cf4cf-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cf4cf-134">Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="cf4cf-135">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="cf4cf-136">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="cf4cf-137">Для работы с Blazor WebAssembly выберите шаблон **Приложение Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="cf4cf-138">Для работы с Blazor Server выберите шаблон **Серверное приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="cf4cf-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="cf4cf-139">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-139">Select **Next**.</span></span>

   <span data-ttu-id="cf4cf-140">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cf4cf-141">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="cf4cf-142">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="cf4cf-143">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="cf4cf-144">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-144">Select **Next**.</span></span>

1. <span data-ttu-id="cf4cf-145">В поле **Имя проекта** присвойте имя приложению `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="cf4cf-146">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-146">Select **Create**.</span></span>

1. <span data-ttu-id="cf4cf-147">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="cf4cf-148">Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="cf4cf-149">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="cf4cf-150">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cf4cf-151">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="cf4cf-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="cf4cf-152">Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="cf4cf-153">Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="cf4cf-154">Для работы с Blazor WebAssembly выполните следующие команды в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cf4cf-155">Для работы с Blazor Server выполните следующие команды в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cf4cf-156">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cf4cf-157">В браузере перейдите на адрес `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="cf4cf-158">На вкладках на боковой панели доступно несколько страниц:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="cf4cf-159">Главная страница</span><span class="sxs-lookup"><span data-stu-id="cf4cf-159">Home</span></span>
* <span data-ttu-id="cf4cf-160">Счетчик</span><span class="sxs-lookup"><span data-stu-id="cf4cf-160">Counter</span></span>
* <span data-ttu-id="cf4cf-161">Выборка данных</span><span class="sxs-lookup"><span data-stu-id="cf4cf-161">Fetch data</span></span>

<span data-ttu-id="cf4cf-162">На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="cf4cf-163">Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript, однако с Blazor можно использовать C#.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="cf4cf-164">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="cf4cf-165">Запрос `/counter` в браузере, как указано в директиве `@page` в верхней части, заставляет компонент `Counter` визуализировать свое содержимое.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="cf4cf-166">Компоненты преобразуются в представление дерева визуализации. Его затем можно использовать для гибкого и эффективного обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="cf4cf-167">Каждый раз при нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="cf4cf-168">Запускается событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="cf4cf-169">вызывается метод `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="cf4cf-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="cf4cf-170">Увеличивается значение `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="cf4cf-171">Компонент визуализируется снова.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-171">The component is rendered again.</span></span>

<span data-ttu-id="cf4cf-172">Среда выполнения сравнивает новое содержимое с предыдущим содержимым и применяет к модели DOM только измененное содержимое.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="cf4cf-173">Добавьте компонент в другой компонент, используя синтаксис HTML.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="cf4cf-174">Например, добавьте компонент `Counter` на домашнюю страницу приложения, разместив элемент `<Counter />` внутри компонента `Index`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="cf4cf-175">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="cf4cf-176">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-176">Run the app.</span></span> <span data-ttu-id="cf4cf-177">На домашней странице имеется собственный счетчик, предоставленный компонентом `Counter`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="cf4cf-178">Параметры компонента указываются с помощью атрибутов или [дочернего содержимого](xref:blazor/components#child-content), которые позволяют задавать свойства дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-178">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="cf4cf-179">Чтобы добавить параметр в компонент `Counter`, обновите блок `@code`компонента:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="cf4cf-180">Добавьте открытое свойство для `IncrementAmount` с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="cf4cf-181">Изменение метод `IncrementCount`, чтобы он использовал `IncrementAmount` при увеличении значения `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="cf4cf-182">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="cf4cf-183">Укажите `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="cf4cf-184">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="cf4cf-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="cf4cf-185">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-185">Run the app.</span></span> <span data-ttu-id="cf4cf-186">Теперь у компонента `Index` будет собственный счетчик, который будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь).</span><span class="sxs-lookup"><span data-stu-id="cf4cf-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="cf4cf-187">Компонент `Counter` (*Counter.razor*) в компоненте `/counter` продолжает увеличиваться на единицу.</span><span class="sxs-lookup"><span data-stu-id="cf4cf-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cf4cf-188">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="cf4cf-188">Next steps</span></span>

<span data-ttu-id="cf4cf-189">Пошаговое создание приложения Blazor</span><span class="sxs-lookup"><span data-stu-id="cf4cf-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="cf4cf-190">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="cf4cf-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
