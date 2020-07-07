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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 5a58e7ae28842e2e8a0f3bae8f47e252839903fe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408881"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="6bd12-103">Использование SignalR для ASP.NET Core с Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6bd12-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="6bd12-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6bd12-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6bd12-105">В этом руководстве описаны основы того, как с помощью SignalR и Blazor WebAssembly создать приложение, работающее в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="6bd12-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="6bd12-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="6bd12-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6bd12-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="6bd12-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6bd12-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="6bd12-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="6bd12-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="6bd12-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="6bd12-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="6bd12-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6bd12-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="6bd12-111">Add Razor component code for chat</span></span>

<span data-ttu-id="6bd12-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="6bd12-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="6bd12-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bd12-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6bd12-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="6bd12-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bd12-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bd12-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6bd12-116">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="6bd12-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6bd12-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6bd12-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6bd12-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6bd12-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6bd12-119">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="6bd12-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6bd12-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="6bd12-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="6bd12-121">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6bd12-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="6bd12-122">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="6bd12-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bd12-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bd12-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="6bd12-124">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="6bd12-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="6bd12-125">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="6bd12-125">Create a new project.</span></span>

1. <span data-ttu-id="6bd12-126">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="6bd12-127">Введите `BlazorSignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="6bd12-128">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="6bd12-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6bd12-129">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-129">Select **Create**.</span></span>

1. <span data-ttu-id="6bd12-130">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="6bd12-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="6bd12-131">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="6bd12-132">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6bd12-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6bd12-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6bd12-134">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="6bd12-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="6bd12-135">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6bd12-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="6bd12-136">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="6bd12-137">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6bd12-138">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6bd12-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6bd12-139">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="6bd12-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="6bd12-140">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="6bd12-141">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="6bd12-142">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="6bd12-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6bd12-143">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-143">Select **Next**.</span></span>

   <span data-ttu-id="6bd12-144">Подтвердите следующие конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6bd12-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="6bd12-145">Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="6bd12-146">Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="6bd12-147">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="6bd12-148">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-148">Select **Next**.</span></span>

1. <span data-ttu-id="6bd12-149">В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="6bd12-150">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-150">Select **Create**.</span></span>

   <span data-ttu-id="6bd12-151">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="6bd12-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="6bd12-152">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="6bd12-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="6bd12-153">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="6bd12-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6bd12-154">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="6bd12-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6bd12-155">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="6bd12-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="6bd12-156">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="6bd12-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bd12-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bd12-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="6bd12-158">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6bd12-159">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6bd12-160">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="6bd12-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6bd12-161">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="6bd12-162">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="6bd12-163">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="6bd12-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6bd12-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6bd12-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="6bd12-165">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="6bd12-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6bd12-166">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6bd12-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6bd12-167">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6bd12-168">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6bd12-169">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="6bd12-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6bd12-170">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="6bd12-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="6bd12-171">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="6bd12-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6bd12-172">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="6bd12-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6bd12-173">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="6bd12-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="6bd12-174">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="6bd12-174">Add a SignalR hub</span></span>

<span data-ttu-id="6bd12-175">В проекте `BlazorSignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="6bd12-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="6bd12-176">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="6bd12-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="6bd12-177">В проекте `BlazorSignalRApp.Server` откройте файл `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="6bd12-178">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="6bd12-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="6bd12-179">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6bd12-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="6bd12-180">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6bd12-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="6bd12-181">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="6bd12-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="6bd12-182">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="6bd12-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="6bd12-183">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="6bd12-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="6bd12-184">В проекте `BlazorSignalRApp.Client` откройте файл `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="6bd12-185">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6bd12-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="6bd12-186">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="6bd12-186">Run the app</span></span>

1. <span data-ttu-id="6bd12-187">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="6bd12-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6bd12-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bd12-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6bd12-189">В **обозревателе решений** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6bd12-190">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="6bd12-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6bd12-191">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6bd12-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6bd12-192">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bd12-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6bd12-193">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="6bd12-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6bd12-194">Пример приложения Blazor WebAssembly для ![SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6bd12-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6bd12-195">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6bd12-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6bd12-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6bd12-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6bd12-197">Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="6bd12-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="6bd12-198">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="6bd12-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6bd12-199">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6bd12-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6bd12-200">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bd12-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6bd12-201">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="6bd12-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6bd12-202">Пример приложения Blazor WebAssembly для ![SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6bd12-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6bd12-203">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6bd12-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6bd12-204">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6bd12-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6bd12-205">На боковой панели **Решение** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="6bd12-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6bd12-206">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="6bd12-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6bd12-207">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6bd12-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6bd12-208">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bd12-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6bd12-209">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="6bd12-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6bd12-210">Пример приложения Blazor WebAssembly для ![SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6bd12-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6bd12-211">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6bd12-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6bd12-212">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="6bd12-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6bd12-213">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="6bd12-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="6bd12-214">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6bd12-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6bd12-215">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="6bd12-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6bd12-216">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="6bd12-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6bd12-217">Пример приложения Blazor WebAssembly для ![SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6bd12-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6bd12-218">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6bd12-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="6bd12-219">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="6bd12-219">Next steps</span></span>

<span data-ttu-id="6bd12-220">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="6bd12-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6bd12-221">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="6bd12-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6bd12-222">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="6bd12-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="6bd12-223">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="6bd12-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="6bd12-224">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="6bd12-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6bd12-225">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="6bd12-225">Add Razor component code for chat</span></span>

<span data-ttu-id="6bd12-226">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="6bd12-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="6bd12-227">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6bd12-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="6bd12-228">[Согласование независимо от источника для проверки подлинности для SignalR](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="6bd12-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
