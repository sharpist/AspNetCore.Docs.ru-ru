---
title: Пакет размещения
author: rick-anderson
description: Узнайте, как настроить пакет размещения .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755178"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="2e1cc-103">Пакет размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="2e1cc-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="2e1cc-104">Пакет размещения .NET Core — это установщик среды выполнения .NET Core и [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="2e1cc-105">Пакет позволяет запускать приложения ASP.NET Core в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="2e1cc-106">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="2e1cc-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e1cc-107">Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="2e1cc-108">После установки служб IIS запустите установщик пакета размещения еще раз.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="2e1cc-109">Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="2e1cc-110">Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="2e1cc-111">Прямая загрузка (текущая версия)</span><span class="sxs-lookup"><span data-stu-id="2e1cc-111">Direct download (current version)</span></span>

<span data-ttu-id="2e1cc-112">Скачайте установщик по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="2e1cc-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="2e1cc-113">Текущий установщик пакета размещения .NET Core (прямая загрузка)</span><span class="sxs-lookup"><span data-stu-id="2e1cc-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="2e1cc-114">Более ранние версии установщика</span><span class="sxs-lookup"><span data-stu-id="2e1cc-114">Earlier versions of the installer</span></span>

<span data-ttu-id="2e1cc-115">Получение более ранней версии установщика:</span><span class="sxs-lookup"><span data-stu-id="2e1cc-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="2e1cc-116">Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="2e1cc-117">Выберите требуемую версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="2e1cc-118">В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="2e1cc-119">Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="2e1cc-120">Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="2e1cc-121">Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="2e1cc-122">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="2e1cc-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="2e1cc-123">Запустите установщик на сервере.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-123">Run the installer on the server.</span></span> <span data-ttu-id="2e1cc-124">При запуске установщика из командной оболочки администратора доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="2e1cc-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="2e1cc-125">`OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="2e1cc-126">`OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="2e1cc-127">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2e1cc-128">`OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="2e1cc-129">Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="2e1cc-130">`OPT_NO_X86=1`. Пропуск установки сред выполнения x86.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="2e1cc-131">Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="2e1cc-132">Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="2e1cc-133">`OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (`applicationHost.config`) находится на том же компьютере, что и установка IIS.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="2e1cc-134">*Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="2e1cc-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="2e1cc-135">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="2e1cc-136">Перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2e1cc-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="2e1cc-137">Перезапуск служб IIS позволит обнаружить внесенные установщиком изменения в системном пути, который является переменной среды.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="2e1cc-138">ASP.NET Core не наследует поведение наката для выпусков исправлений пакетов общей платформы.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="2e1cc-139">После обновления общей платформы путем установки нового пакета размещения перезапустите систему или выполните в командной оболочке следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2e1cc-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="2e1cc-140">Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="2e1cc-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2e1cc-141">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="2e1cc-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2e1cc-142">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2e1cc-143">В системе размещения перейдите к папке `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="2e1cc-144">Найдите файл `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="2e1cc-145">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2e1cc-146">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2e1cc-147">Журналы установщика пакета размещения для модуля находятся в папке `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="2e1cc-148">Файл имеет имя `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, где заполнитель `{TIMESTAMP}` — это метка времени файла.</span><span class="sxs-lookup"><span data-stu-id="2e1cc-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
