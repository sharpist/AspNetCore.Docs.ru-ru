---
title: Использование SignalR для ASP.NET Core с Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773792"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="42e22-103">Использование SignalR для ASP.NET Core с Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="42e22-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="42e22-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="42e22-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="42e22-105">В этом руководстве показано, как создать приложение, работающее в реальном времени, с помощью SignalR и Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="42e22-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="42e22-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="42e22-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42e22-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="42e22-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="42e22-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="42e22-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="42e22-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="42e22-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="42e22-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="42e22-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="42e22-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="42e22-111">Add Razor component code for chat</span></span>

<span data-ttu-id="42e22-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="42e22-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="42e22-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42e22-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42e22-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="42e22-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42e22-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e22-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="42e22-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42e22-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42e22-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42e22-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="42e22-118">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e22-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="42e22-119">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="42e22-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="42e22-120">Если вы не используете предварительную версию 2 Visual Studio 16.6 и более позднюю, установите шаблон [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="42e22-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="42e22-121">В период действия предварительной версии Blazor WebAssembly будет использоваться предварительная версия пакета [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/).</span><span class="sxs-lookup"><span data-stu-id="42e22-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="42e22-122">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42e22-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

<span data-ttu-id="42e22-123">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="42e22-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42e22-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e22-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="42e22-125">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="42e22-125">Create a new project.</span></span>

1. <span data-ttu-id="42e22-126">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="42e22-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="42e22-127">В поле **Имя проекта** введите BlazorSignalRApp.</span><span class="sxs-lookup"><span data-stu-id="42e22-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="42e22-128">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="42e22-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="42e22-129">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42e22-129">Select **Create**.</span></span>

1. <span data-ttu-id="42e22-130">Выберите шаблон **приложения Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="42e22-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="42e22-131">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="42e22-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="42e22-132">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="42e22-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="42e22-133">Если вы обновили или установили новую версию Visual Studio, а шаблон Blazor WebAssembly не отображается в пользовательском интерфейсе VS, переустановите шаблон с помощью команды `dotnet new` (см. выше).</span><span class="sxs-lookup"><span data-stu-id="42e22-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="42e22-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42e22-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="42e22-135">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42e22-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="42e22-136">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="42e22-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="42e22-137">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="42e22-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="42e22-138">Visual Studio Code автоматически добавит папку *.vscode* с файлами *launch.json* и *tasks.json*.</span><span class="sxs-lookup"><span data-stu-id="42e22-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42e22-139">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42e22-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="42e22-140">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42e22-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="42e22-141">В Visual Studio для Mac откройте проект. Для этого перейдите к его папке и откройте файл решения проекта ( *.sln*).</span><span class="sxs-lookup"><span data-stu-id="42e22-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="42e22-142">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e22-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="42e22-143">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="42e22-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="42e22-144">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="42e22-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42e22-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e22-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="42e22-146">В **обозревателе решений** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="42e22-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="42e22-147">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="42e22-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="42e22-148">Выберите **Обзор** и введите в поле поиска Microsoft.AspNetCore.SignalR.Client.</span><span class="sxs-lookup"><span data-stu-id="42e22-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="42e22-149">В результатах поиска выберите пакет `Microsoft.AspNetCore.SignalR.Client` и щелкните **Установить**.</span><span class="sxs-lookup"><span data-stu-id="42e22-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="42e22-150">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="42e22-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="42e22-151">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="42e22-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="42e22-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42e22-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="42e22-153">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="42e22-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42e22-154">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42e22-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="42e22-155">На боковой панели **Решение** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="42e22-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="42e22-156">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="42e22-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="42e22-157">Выберите **Обзор** и введите в поле поиска Microsoft.AspNetCore.SignalR.Client.</span><span class="sxs-lookup"><span data-stu-id="42e22-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="42e22-158">В результатах поиска установите флажок рядом с пакетом `Microsoft.AspNetCore.SignalR.Client` и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="42e22-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="42e22-159">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="42e22-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="42e22-160">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e22-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="42e22-161">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="42e22-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="42e22-162">Добавление концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="42e22-162">Add a SignalR hub</span></span>

<span data-ttu-id="42e22-163">В проекте **BlazorSignalRApp.Server** создайте папку *Hubs* и добавьте следующий класс `ChatHub` (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="42e22-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="42e22-164">Добавьте службы и конечную точку для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="42e22-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="42e22-165">В проекте **BlazorSignalRApp.Server** откройте файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="42e22-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="42e22-166">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="42e22-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="42e22-167">Добавьте службы SignalR и промежуточного ПО для компрессии ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42e22-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="42e22-168">В `Startup.Configure` между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора:</span><span class="sxs-lookup"><span data-stu-id="42e22-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="42e22-169">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="42e22-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="42e22-170">В проекте **BlazorSignalRApp.Client** откройте файл *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="42e22-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="42e22-171">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="42e22-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="42e22-172">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="42e22-172">Run the app</span></span>

1. <span data-ttu-id="42e22-173">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="42e22-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="42e22-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42e22-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="42e22-175">В **обозревателе решений** выберите проект **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="42e22-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="42e22-176">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="42e22-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="42e22-177">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="42e22-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="42e22-178">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="42e22-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="42e22-179">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="42e22-179">The name and message are displayed on both pages instantly:</span></span>

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="42e22-181">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="42e22-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="42e22-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="42e22-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="42e22-183">Когда VS Code предлагает создать профиль запуска для серверного приложения ( *. vscode/Launch. JSON*), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="42e22-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="42e22-184">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="42e22-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="42e22-185">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="42e22-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="42e22-186">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="42e22-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="42e22-187">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="42e22-187">The name and message are displayed on both pages instantly:</span></span>

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="42e22-189">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="42e22-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="42e22-190">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="42e22-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="42e22-191">На боковой панели **Решение** выберите проект **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="42e22-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="42e22-192">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>\*\*, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="42e22-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="42e22-193">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="42e22-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="42e22-194">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="42e22-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="42e22-195">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="42e22-195">The name and message are displayed on both pages instantly:</span></span>

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="42e22-197">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="42e22-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="42e22-198">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="42e22-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="42e22-199">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="42e22-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="42e22-200">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="42e22-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="42e22-201">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="42e22-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="42e22-202">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="42e22-202">The name and message are displayed on both pages instantly:</span></span>

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="42e22-204">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="42e22-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="42e22-205">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="42e22-205">Next steps</span></span>

<span data-ttu-id="42e22-206">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="42e22-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="42e22-207">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="42e22-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="42e22-208">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="42e22-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="42e22-209">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="42e22-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="42e22-210">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="42e22-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="42e22-211">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="42e22-211">Add Razor component code for chat</span></span>

<span data-ttu-id="42e22-212">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="42e22-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="42e22-213">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="42e22-213">Additional resources</span></span>

* <xref:signalr/introduction>
