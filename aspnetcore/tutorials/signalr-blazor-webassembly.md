---
title: Использование SignalR для ASP.NET Core с Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508179"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="1d6e6-103">Использование SignalR для ASP.NET Core с Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1d6e6-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="1d6e6-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d6e6-105">В этом руководстве описаны основы того, как с помощью SignalR и Blazor WebAssembly создать приложение, работающее в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="1d6e6-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1d6e6-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="1d6e6-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="1d6e6-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="1d6e6-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="1d6e6-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1d6e6-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-111">Add Razor component code for chat</span></span>

<span data-ttu-id="1d6e6-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="1d6e6-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1d6e6-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d6e6-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="1d6e6-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1d6e6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d6e6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1d6e6-116">[Visual Studio 2019 16.8 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="1d6e6-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d6e6-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d6e6-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d6e6-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1d6e6-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1d6e6-119">Visual Studio для Mac 8.8 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="1d6e6-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1d6e6-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1d6e6-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1d6e6-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d6e6-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1d6e6-122">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="1d6e6-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d6e6-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d6e6-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d6e6-124">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1d6e6-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1d6e6-125">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="1d6e6-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1d6e6-126">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1d6e6-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="1d6e6-127">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1d6e6-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="1d6e6-128">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d6e6-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d6e6-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1d6e6-130">Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1d6e6-131">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="1d6e6-132">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-132">Create a new project.</span></span>

1. <span data-ttu-id="1d6e6-133">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="1d6e6-134">Введите `BlazorSignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="1d6e6-135">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1d6e6-136">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-136">Select **Create**.</span></span>

1. <span data-ttu-id="1d6e6-137">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="1d6e6-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="1d6e6-138">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="1d6e6-139">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d6e6-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d6e6-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1d6e6-141">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="1d6e6-142">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="1d6e6-143">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="1d6e6-144">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d6e6-145">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1d6e6-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1d6e6-146">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="1d6e6-147">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1d6e6-148">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="1d6e6-149">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="1d6e6-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1d6e6-150">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-150">Select **Next**.</span></span>

1. <span data-ttu-id="1d6e6-151">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="1d6e6-152">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="1d6e6-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-153">Select **Next**.</span></span>

1. <span data-ttu-id="1d6e6-154">В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="1d6e6-155">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-155">Select **Create**.</span></span>

   <span data-ttu-id="1d6e6-156">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1d6e6-157">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="1d6e6-158">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="1d6e6-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1d6e6-159">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1d6e6-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1d6e6-160">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="1d6e6-161">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="1d6e6-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d6e6-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d6e6-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1d6e6-163">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1d6e6-164">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1d6e6-165">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1d6e6-166">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="1d6e6-167">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="1d6e6-168">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d6e6-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d6e6-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1d6e6-170">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d6e6-171">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1d6e6-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1d6e6-172">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1d6e6-173">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1d6e6-174">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1d6e6-175">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="1d6e6-176">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1d6e6-177">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1d6e6-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1d6e6-178">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="1d6e6-179">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-179">Add a SignalR hub</span></span>

<span data-ttu-id="1d6e6-180">В проекте `BlazorSignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="1d6e6-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="1d6e6-181">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="1d6e6-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="1d6e6-182">В проекте `BlazorSignalRApp.Server` откройте файл `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="1d6e6-183">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="1d6e6-184">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="1d6e6-185">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1d6e6-186">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1d6e6-187">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="1d6e6-188">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="1d6e6-189">В проекте `BlazorSignalRApp.Client` откройте файл `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="1d6e6-190">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="1d6e6-191">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="1d6e6-191">Run the app</span></span>

1. <span data-ttu-id="1d6e6-192">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1d6e6-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d6e6-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1d6e6-194">В **обозревателе решений** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-194">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="1d6e6-195">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1d6e6-196">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1d6e6-197">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1d6e6-198">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-198">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1d6e6-200">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1d6e6-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d6e6-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1d6e6-202">Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="1d6e6-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="1d6e6-203">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1d6e6-204">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1d6e6-205">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1d6e6-206">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-206">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1d6e6-208">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1d6e6-209">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1d6e6-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1d6e6-210">На боковой панели **Решение** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="1d6e6-211">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1d6e6-212">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1d6e6-213">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1d6e6-214">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-214">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1d6e6-216">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1d6e6-217">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="1d6e6-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="1d6e6-218">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="1d6e6-219">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1d6e6-220">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1d6e6-221">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-221">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="1d6e6-223">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="1d6e6-224">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="1d6e6-224">Next steps</span></span>

<span data-ttu-id="1d6e6-225">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1d6e6-226">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="1d6e6-227">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="1d6e6-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="1d6e6-228">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="1d6e6-229">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="1d6e6-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1d6e6-230">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="1d6e6-230">Add Razor component code for chat</span></span>

<span data-ttu-id="1d6e6-231">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="1d6e6-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="1d6e6-232"><xref:blazor/index>
> [Проверка подлинности маркера носителя с помощью сервера Identity, WebSockets и отправляемыми сервером событиями](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="1d6e6-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d6e6-233">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1d6e6-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="1d6e6-234">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="1d6e6-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
