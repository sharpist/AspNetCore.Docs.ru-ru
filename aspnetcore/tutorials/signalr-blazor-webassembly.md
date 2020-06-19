---
title: Использование SignalR для ASP.NET Core с Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102704"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="dbcc1-103">Использование SignalR для ASP.NET Core с Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="dbcc1-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="dbcc1-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dbcc1-105">В этом руководстве описаны основы создания приложения в режиме реального времени с помощью SignalR с Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="dbcc1-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbcc1-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="dbcc1-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="dbcc1-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="dbcc1-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="dbcc1-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="dbcc1-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-111">Add Razor component code for chat</span></span>

<span data-ttu-id="dbcc1-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="dbcc1-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dbcc1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dbcc1-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="dbcc1-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbcc1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbcc1-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dbcc1-116">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="dbcc1-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbcc1-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbcc1-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbcc1-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="dbcc1-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="dbcc1-119">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="dbcc1-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="dbcc1-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcc1-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="dbcc1-121">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="dbcc1-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="dbcc1-122">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbcc1-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbcc1-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="dbcc1-124">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="dbcc1-125">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-125">Create a new project.</span></span>

1. <span data-ttu-id="dbcc1-126">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="dbcc1-127">В поле **Имя проекта** введите BlazorSignalRApp.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="dbcc1-128">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="dbcc1-129">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-129">Select **Create**.</span></span>

1. <span data-ttu-id="dbcc1-130">Выберите шаблон **приложения Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="dbcc1-131">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="dbcc1-132">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="dbcc1-133">Если вы обновили или установили новую версию Visual Studio, а шаблон Blazor WebAssembly не отображается в пользовательском интерфейсе VS, переустановите шаблон с помощью команды `dotnet new` (см. выше).</span><span class="sxs-lookup"><span data-stu-id="dbcc1-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbcc1-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbcc1-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dbcc1-135">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="dbcc1-136">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="dbcc1-137">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="dbcc1-138">Visual Studio Code автоматически добавит папку *.vscode* с файлами *launch.json* и *tasks.json*.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbcc1-139">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="dbcc1-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbcc1-140">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="dbcc1-141">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="dbcc1-142">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="dbcc1-143">Выберите шаблон **приложения Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="dbcc1-144">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-144">Select **Next**.</span></span>

   <span data-ttu-id="dbcc1-145">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="dbcc1-146">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="dbcc1-147">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="dbcc1-148">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="dbcc1-149">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-149">Select **Next**.</span></span>

1. <span data-ttu-id="dbcc1-150">В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="dbcc1-151">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-151">Select **Create**.</span></span>

   <span data-ttu-id="dbcc1-152">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="dbcc1-153">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="dbcc1-154">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (*SLN*).</span><span class="sxs-lookup"><span data-stu-id="dbcc1-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbcc1-155">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcc1-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="dbcc1-156">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="dbcc1-157">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="dbcc1-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbcc1-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbcc1-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="dbcc1-159">В **обозревателе решений** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="dbcc1-160">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="dbcc1-161">Выберите **Обзор** и введите в поле поиска "Microsoft.AspNetCore.SignalR.Client".</span><span class="sxs-lookup"><span data-stu-id="dbcc1-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="dbcc1-162">В результатах поиска выберите пакет [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) и щелкните **Установить**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="dbcc1-163">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="dbcc1-164">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbcc1-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbcc1-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="dbcc1-166">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbcc1-167">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="dbcc1-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbcc1-168">На боковой панели **Решение** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="dbcc1-169">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="dbcc1-170">Выберите **Обзор** и введите в поле поиска "Microsoft.AspNetCore.SignalR.Client".</span><span class="sxs-lookup"><span data-stu-id="dbcc1-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="dbcc1-171">В результатах поиска установите флажок рядом с пакетом [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="dbcc1-172">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbcc1-173">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcc1-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="dbcc1-174">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="dbcc1-175">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-175">Add a SignalR hub</span></span>

<span data-ttu-id="dbcc1-176">В проекте **BlazorSignalRApp.Server** создайте папку *Hubs* и добавьте следующий класс `ChatHub` (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="dbcc1-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="dbcc1-177">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="dbcc1-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="dbcc1-178">В проекте **BlazorSignalRApp.Server** откройте файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="dbcc1-179">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="dbcc1-180">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="dbcc1-181">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="dbcc1-182">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="dbcc1-183">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="dbcc1-184">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="dbcc1-185">В проекте **BlazorSignalRApp.Client** откройте файл *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="dbcc1-186">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="dbcc1-187">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="dbcc1-187">Run the app</span></span>

1. <span data-ttu-id="dbcc1-188">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dbcc1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbcc1-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dbcc1-190">В **обозревателе решений** выберите проект **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="dbcc1-191">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="dbcc1-192">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dbcc1-193">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="dbcc1-194">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="dbcc1-195">![SignalR Пример приложения Blazor WebAssembly в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="dbcc1-196">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dbcc1-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbcc1-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dbcc1-198">Когда VS Code предлагает создать профиль запуска для серверного приложения ( *. vscode/Launch. JSON*), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="dbcc1-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="dbcc1-199">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="dbcc1-200">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dbcc1-201">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="dbcc1-202">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="dbcc1-203">![SignalR Пример приложения Blazor WebAssembly в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="dbcc1-204">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dbcc1-205">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="dbcc1-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbcc1-206">На боковой панели **Решение** выберите проект **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="dbcc1-207">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="dbcc1-208">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dbcc1-209">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="dbcc1-210">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="dbcc1-211">![SignalR Пример приложения Blazor WebAssembly в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="dbcc1-212">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="dbcc1-213">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcc1-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="dbcc1-214">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="dbcc1-215">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dbcc1-216">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="dbcc1-217">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="dbcc1-218">![SignalR Пример приложения Blazor WebAssembly в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="dbcc1-219">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="dbcc1-220">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="dbcc1-220">Next steps</span></span>

<span data-ttu-id="dbcc1-221">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbcc1-222">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="dbcc1-223">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="dbcc1-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="dbcc1-224">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="dbcc1-225">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="dbcc1-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="dbcc1-226">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="dbcc1-226">Add Razor component code for chat</span></span>

<span data-ttu-id="dbcc1-227">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="dbcc1-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="dbcc1-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dbcc1-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="dbcc1-229">[Согласование независимо от источника для проверки подлинности для SignalR](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="dbcc1-229">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
