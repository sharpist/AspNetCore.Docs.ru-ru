---
title: Пакет размещения
author: rick-anderson
description: Узнайте, как настроить пакет размещения .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343641"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="1321f-103">Пакет размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="1321f-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="1321f-104">Пакет размещения .NET Core — это установщик среды выполнения .NET Core и [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="1321f-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="1321f-105">Пакет позволяет запускать приложения ASP.NET Core в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="1321f-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="1321f-106">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="1321f-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1321f-107">Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить.</span><span class="sxs-lookup"><span data-stu-id="1321f-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="1321f-108">После установки служб IIS запустите установщик пакета размещения еще раз.</span><span class="sxs-lookup"><span data-stu-id="1321f-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="1321f-109">Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="1321f-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="1321f-110">Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="1321f-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="1321f-111">Прямая загрузка (текущая версия)</span><span class="sxs-lookup"><span data-stu-id="1321f-111">Direct download (current version)</span></span>

<span data-ttu-id="1321f-112">Скачайте установщик по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="1321f-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="1321f-113">Текущий установщик пакета размещения .NET Core (прямая загрузка)</span><span class="sxs-lookup"><span data-stu-id="1321f-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="1321f-114">Требование к установке распространяемого компонента Visual C++</span><span class="sxs-lookup"><span data-stu-id="1321f-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="1321f-115">В старых версиях Windows Server 2012 R2 установите распространяемый компонент Visual Studio C++ 2015, 2017 или 2019.</span><span class="sxs-lookup"><span data-stu-id="1321f-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="1321f-116">Если этого не сделать, в журнале событий Windows будет появляться непонятная ошибка `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="1321f-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="1321f-117">[Текущая 64-разрядная версия распространяемого компонента VS C++](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Текущая 32-битная версия распространяемого компонента VS C++](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="1321f-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="1321f-118">Более ранние версии установщика</span><span class="sxs-lookup"><span data-stu-id="1321f-118">Earlier versions of the installer</span></span>

<span data-ttu-id="1321f-119">Получение более ранней версии установщика:</span><span class="sxs-lookup"><span data-stu-id="1321f-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="1321f-120">Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="1321f-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="1321f-121">Выберите требуемую версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1321f-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="1321f-122">В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1321f-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="1321f-123">Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).</span><span class="sxs-lookup"><span data-stu-id="1321f-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="1321f-124">Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="1321f-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="1321f-125">Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="1321f-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="1321f-126">Параметры</span><span class="sxs-lookup"><span data-stu-id="1321f-126">Options</span></span>

1. <span data-ttu-id="1321f-127">При запуске установщика из командной оболочки администратора доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="1321f-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="1321f-128">`OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1321f-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="1321f-129">`OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1321f-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="1321f-130">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="1321f-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="1321f-131">`OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="1321f-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="1321f-132">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="1321f-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="1321f-133">`OPT_NO_X86=1`. Пропуск установки сред выполнения x86.</span><span class="sxs-lookup"><span data-stu-id="1321f-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="1321f-134">Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения.</span><span class="sxs-lookup"><span data-stu-id="1321f-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="1321f-135">Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="1321f-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="1321f-136">`OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (`applicationHost.config`) находится на том же компьютере, что и установка IIS.</span><span class="sxs-lookup"><span data-stu-id="1321f-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="1321f-137">*Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="1321f-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="1321f-138">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="1321f-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="1321f-139">Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="1321f-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="1321f-140">Перезапустить IIS</span><span class="sxs-lookup"><span data-stu-id="1321f-140">Restart IIS</span></span>

<span data-ttu-id="1321f-141">После установки пакета размещения может потребоваться установить службы IIS вручную.</span><span class="sxs-lookup"><span data-stu-id="1321f-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="1321f-142">Например, инструментарий CLI (команда) `dotnet` может не существовать по пути PATH для выполнения рабочих процессов служб IIS.</span><span class="sxs-lookup"><span data-stu-id="1321f-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="1321f-143">Чтобы вручную остановить и запустить службы IIS, выполните следующие команды в командной строке с повышенными разрешениями:</span><span class="sxs-lookup"><span data-stu-id="1321f-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1321f-144">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="1321f-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1321f-145">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="1321f-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1321f-146">В системе размещения перейдите к папке `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="1321f-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="1321f-147">Найдите файл `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="1321f-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="1321f-148">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="1321f-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1321f-149">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="1321f-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1321f-150">Журналы установщика пакета размещения для модуля находятся в папке `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="1321f-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="1321f-151">Файл имеет имя `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, где заполнитель `{TIMESTAMP}` — это метка времени файла.</span><span class="sxs-lookup"><span data-stu-id="1321f-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
