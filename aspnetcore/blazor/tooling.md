---
title: Инструменты для Blazor ASP.NET Core
author: guardrex
description: Сведения об инструментах, доступных для создания приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 077d8943e424df4d5a14950dfadc2dd73d2ce4d6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013402"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="2eb1a-103">Инструменты для Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2eb1a-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2eb1a-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2eb1a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="2eb1a-105">Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="2eb1a-106">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-106">Create a new project.</span></span>

1. <span data-ttu-id="2eb1a-107">Выберите **Приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="2eb1a-107">Select **Blazor App**.</span></span> <span data-ttu-id="2eb1a-108">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-108">Select **Next**.</span></span>

1. <span data-ttu-id="2eb1a-109">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2eb1a-110">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2eb1a-111">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-111">Select **Create**.</span></span>

1. <span data-ttu-id="2eb1a-112">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="2eb1a-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2eb1a-113">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="2eb1a-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2eb1a-114">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-114">Select **Create**.</span></span>

   <span data-ttu-id="2eb1a-115">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2eb1a-116">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="2eb1a-117">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="2eb1a-118">Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2eb1a-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="2eb1a-119">Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="2eb1a-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="2eb1a-120">Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2eb1a-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="2eb1a-121">Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="2eb1a-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="2eb1a-122">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="2eb1a-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="2eb1a-123">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="2eb1a-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="2eb1a-124">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2eb1a-125">Откройте папку `WebApplication1` в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="2eb1a-126">Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2eb1a-127">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-127">Select **Yes**.</span></span>

1. <span data-ttu-id="2eb1a-128">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="2eb1a-129">Установка доверия к сертификату разработки</span><span class="sxs-lookup"><span data-stu-id="2eb1a-129">Trust a development certificate</span></span>

<span data-ttu-id="2eb1a-130">В Linux отсутствует централизованный способ установки доверия к сертификату.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="2eb1a-131">Обычно используется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="2eb1a-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="2eb1a-132">исключение URL-адреса приложения в списке исключений браузера;</span><span class="sxs-lookup"><span data-stu-id="2eb1a-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="2eb1a-133">установка доверия ко всем самозаверяющим сертификатам для `localhost`;</span><span class="sxs-lookup"><span data-stu-id="2eb1a-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="2eb1a-134">добавление сертификата в список доверенных сертификатов в браузере.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="2eb1a-135">Дополнительные сведения см. в рекомендациях, предоставляемых для вашего браузера и дистрибутива Linux.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="2eb1a-136">Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="2eb1a-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="2eb1a-137">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2eb1a-138">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="2eb1a-139">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="2eb1a-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2eb1a-140">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="2eb1a-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2eb1a-141">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-141">Select **Next**.</span></span>

   <span data-ttu-id="2eb1a-142">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2eb1a-143">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="2eb1a-144">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-144">Select **Next**.</span></span>

1. <span data-ttu-id="2eb1a-145">В поле **Имя проекта** присвойте имя приложению `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="2eb1a-146">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-146">Select **Create**.</span></span>

1. <span data-ttu-id="2eb1a-147">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="2eb1a-148">Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="2eb1a-149">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2eb1a-150">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="2eb1a-151">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="2eb1a-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
