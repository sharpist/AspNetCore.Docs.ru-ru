---
title: Размещение ASP.NET Core в службе Windows
author: rick-anderson
description: Узнайте, как разместить приложение ASP.NET Core в службе Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 7740774cad33418489fc1d94240574167f84fae6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015365"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="ff20c-103">Размещение ASP.NET Core в службе Windows</span><span class="sxs-lookup"><span data-stu-id="ff20c-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff20c-104">Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="ff20c-105">При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="ff20c-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="ff20c-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff20c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff20c-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ff20c-107">Prerequisites</span></span>

* [<span data-ttu-id="ff20c-108">Пакет SDK для ASP.NET Core 2.1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="ff20c-109">PowerShell 6.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="ff20c-110">Шаблон службы рабочей роли</span><span class="sxs-lookup"><span data-stu-id="ff20c-110">Worker Service template</span></span>

<span data-ttu-id="ff20c-111">Шаблон службы рабочей роли ASP.NET Core может служить отправной точкой для написания длительно выполняющихся приложений служб.</span><span class="sxs-lookup"><span data-stu-id="ff20c-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="ff20c-112">Чтобы использовать шаблон в качестве основы для приложения службы Windows, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="ff20c-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="ff20c-113">Создайте приложение службы рабочей роли на основе шаблона .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="ff20c-114">Согласно указаниям в разделе [Конфигурация приложения](#app-configuration) измените приложение службы рабочей роли так, чтобы оно могло выполняться как служба Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="ff20c-115">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="ff20c-115">App configuration</span></span>

<span data-ttu-id="ff20c-116">Приложению требуется ссылка на пакет для [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="ff20c-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="ff20c-117">`IHostBuilder.UseWindowsService` вызывается при сборке узла.</span><span class="sxs-lookup"><span data-stu-id="ff20c-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="ff20c-118">Если приложение выполняется как служба Windows, метод отвечает за следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ff20c-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="ff20c-119">Задает для узла время существования `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="ff20c-120">Задает для [корневого каталога содержимого](xref:fundamentals/index#content-root) значение [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="ff20c-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="ff20c-121">Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="ff20c-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="ff20c-122">Активирует ведение журнала событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="ff20c-123">В качестве имени источника по умолчанию используется имя приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="ff20c-124">Уровень ведения журнала по умолчанию — как минимум *Предупреждение* для приложения на базе шаблона ASP.NET Core, вызывающего `CreateDefaultBuilder` для сборки узла.</span><span class="sxs-lookup"><span data-stu-id="ff20c-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="ff20c-125">Уровень ведения журнала по умолчанию переопределяется с помощью ключа `Logging:EventLog:LogLevel:Default` в *appsettings.json*/*appsettings.{Environment}.json* или в другом поставщике конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ff20c-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="ff20c-126">Только администраторы могут создавать источники событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="ff20c-127">Если источник событий создать нельзя, используя имя приложения, для источника *Приложение* регистрируется предупреждение и журналы событий отключаются.</span><span class="sxs-lookup"><span data-stu-id="ff20c-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="ff20c-128">В разделе `CreateHostBuilder` файла *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ff20c-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="ff20c-129">Этот раздел сопровождают следующие примеры приложений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="ff20c-130">Пример фоновой службы рабочих ролей. Пример приложения, не являющегося веб-приложением, на основе [шаблона службы рабочих ролей](#worker-service-template), который использует [размещенные службы](xref:fundamentals/host/hosted-services) для фоновых задач.</span><span class="sxs-lookup"><span data-stu-id="ff20c-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="ff20c-131">Пример службы веб-приложений. Пример веб-приложения Razor Pages, который выполняется как служба Windows с [размещенными службами](xref:fundamentals/host/hosted-services) для фоновых задач.</span><span class="sxs-lookup"><span data-stu-id="ff20c-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="ff20c-132">Рекомендации по MVC см. в статьях <xref:mvc/overview> и <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="ff20c-133">Тип развертывания</span><span class="sxs-lookup"><span data-stu-id="ff20c-133">Deployment type</span></span>

<span data-ttu-id="ff20c-134">Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="ff20c-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="ff20c-135">SDK</span><span class="sxs-lookup"><span data-stu-id="ff20c-135">SDK</span></span>

<span data-ttu-id="ff20c-136">Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="ff20c-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ff20c-137">Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="ff20c-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="ff20c-138">Зависящее от платформы развертывание (FDD)</span><span class="sxs-lookup"><span data-stu-id="ff20c-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="ff20c-139">Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="ff20c-140">Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="ff20c-141">При использовании [веб-пакета SDK](#sdk), файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="ff20c-142">Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="ff20c-143">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="ff20c-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="ff20c-144">Для автономного развертывания необязательно наличие общей платформы в системе размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="ff20c-145">Среда выполнения и зависимости приложения развертываются с приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="ff20c-146">[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:</span><span class="sxs-lookup"><span data-stu-id="ff20c-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="ff20c-147">Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="ff20c-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="ff20c-148">Укажите список идентификаторов RID, разделив их точкой с запятой.</span><span class="sxs-lookup"><span data-stu-id="ff20c-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="ff20c-149">Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).</span><span class="sxs-lookup"><span data-stu-id="ff20c-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="ff20c-150">Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ff20c-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="ff20c-151">Учетная запись пользователя службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-151">Service user account</span></span>

<span data-ttu-id="ff20c-152">Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="ff20c-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="ff20c-153">Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="ff20c-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-154">Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="ff20c-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="ff20c-155">Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.</span><span class="sxs-lookup"><span data-stu-id="ff20c-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="ff20c-156">Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).</span><span class="sxs-lookup"><span data-stu-id="ff20c-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="ff20c-157">Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).</span><span class="sxs-lookup"><span data-stu-id="ff20c-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="ff20c-158">Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб.</span><span class="sxs-lookup"><span data-stu-id="ff20c-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="ff20c-159">Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="ff20c-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="ff20c-160">Права на вход в качестве службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-160">Log on as a service rights</span></span>

<span data-ttu-id="ff20c-161">Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="ff20c-162">Откройте редактор локальной политики безопасности, запустив *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="ff20c-163">Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="ff20c-164">Откройте политику **Вход в качестве службы**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="ff20c-165">Щелкните **Добавить пользователя или группу**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="ff20c-166">Укажите имя объекта (учетная запись пользователя) одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="ff20c-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="ff20c-167">Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="ff20c-168">Выберите **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-168">Select **Advanced**.</span></span> <span data-ttu-id="ff20c-169">Нажмите **Найти**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-169">Select **Find Now**.</span></span> <span data-ttu-id="ff20c-170">Выберите учетную запись пользователя из списка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-170">Select the user account from the list.</span></span> <span data-ttu-id="ff20c-171">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-171">Select **OK**.</span></span> <span data-ttu-id="ff20c-172">Нажмите **ОК** еще раз, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="ff20c-173">Нажмите **ОК** или **Применить**, чтобы сохранить изменения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="ff20c-174">Создание службы Windows и управление ею</span><span class="sxs-lookup"><span data-stu-id="ff20c-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="ff20c-175">Создание службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-175">Create a service</span></span>

<span data-ttu-id="ff20c-176">Зарегистрируйте службу с помощью команды PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="ff20c-177">В административной оболочке PowerShell 6 выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="ff20c-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="ff20c-178">`{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="ff20c-179">Не включайте исполняемый файл приложения в путь.</span><span class="sxs-lookup"><span data-stu-id="ff20c-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="ff20c-180">Завершающая косая черта не требуется.</span><span class="sxs-lookup"><span data-stu-id="ff20c-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="ff20c-181">`{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="ff20c-182">`{SERVICE NAME}`. Имя службы (например, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="ff20c-183">`{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="ff20c-184">Включите имя исполняемого файла с расширением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="ff20c-185">`{DESCRIPTION}`. Описание службы (например, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="ff20c-186">`{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="ff20c-187">Запуск службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-187">Start a service</span></span>

<span data-ttu-id="ff20c-188">Запустите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-189">Команде потребуется несколько секунд, чтобы запустить службу.</span><span class="sxs-lookup"><span data-stu-id="ff20c-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="ff20c-190">Определение состояния службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-190">Determine a service's status</span></span>

<span data-ttu-id="ff20c-191">Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-192">Состояние отображается одним из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="ff20c-193">Остановка службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-193">Stop a service</span></span>

<span data-ttu-id="ff20c-194">Остановите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="ff20c-195">Удаление службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-195">Remove a service</span></span>

<span data-ttu-id="ff20c-196">После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ff20c-197">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="ff20c-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ff20c-198">Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="ff20c-199">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="ff20c-200">Настройка конечных точек</span><span class="sxs-lookup"><span data-stu-id="ff20c-200">Configure endpoints</span></span>

<span data-ttu-id="ff20c-201">По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ff20c-202">Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="ff20c-203">Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:</span><span class="sxs-lookup"><span data-stu-id="ff20c-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="ff20c-204">В предыдущем руководстве рассматривается поддержка конечных точек HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="ff20c-205">Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="ff20c-206">Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ff20c-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="ff20c-207">Текущий каталог и корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="ff20c-207">Current directory and content root</span></span>

<span data-ttu-id="ff20c-208">Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory*> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="ff20c-209">Папка *system32* не подходит для хранения файлов службы (например, файлов параметров).</span><span class="sxs-lookup"><span data-stu-id="ff20c-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="ff20c-210">Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.</span><span class="sxs-lookup"><span data-stu-id="ff20c-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="ff20c-211">Использование ContentRootPath или ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="ff20c-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="ff20c-212">Используйте [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) или <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> для поиска ресурсов приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="ff20c-213">Когда приложение запускается как служба, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> задает для свойства <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> значение [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="ff20c-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="ff20c-214">Файлы стандартных параметров приложения *appsettings.json* и *appsettings.{среда}.json* загружаются из корневого каталога содержимого приложения путем вызова [CreateDefaultBuilder во время создания узла](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="ff20c-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="ff20c-215">Для других файлов параметров, загруженных с помощью кода разработчика в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, не нужно вызывать <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="ff20c-216">В следующем примере файл *custom_settings. JSON* существует в корневом каталоге содержимого приложения и загружается без явного указания базового пути:</span><span class="sxs-lookup"><span data-stu-id="ff20c-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="ff20c-217">Не пытайтесь использовать <xref:System.IO.Directory.GetCurrentDirectory*>, чтобы получить путь к ресурсу, так как приложение службы Windows возвращает папку *C:\\WINDOWS\\system32* в качестве текущего каталога.</span><span class="sxs-lookup"><span data-stu-id="ff20c-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="ff20c-218">Хранение файлов службы в подходящем расположении на диске</span><span class="sxs-lookup"><span data-stu-id="ff20c-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="ff20c-219">Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ff20c-220">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ff20c-220">Troubleshoot</span></span>

<span data-ttu-id="ff20c-221">Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="ff20c-222">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="ff20c-222">Common errors</span></span>

* <span data-ttu-id="ff20c-223">Используется старая или предварительная версия PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="ff20c-224">Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="ff20c-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ff20c-225">Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений.</span><span class="sxs-lookup"><span data-stu-id="ff20c-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="ff20c-226">В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:</span><span class="sxs-lookup"><span data-stu-id="ff20c-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="ff20c-227">*bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);</span><span class="sxs-lookup"><span data-stu-id="ff20c-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="ff20c-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).</span><span class="sxs-lookup"><span data-stu-id="ff20c-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="ff20c-229">Служба не находится в состоянии выполнения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="ff20c-230">Пути к используемым приложением ресурсам (например, к сертификатам) неверные.</span><span class="sxs-lookup"><span data-stu-id="ff20c-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="ff20c-231">Базовый путь к службе Windows — *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="ff20c-232">У пользователя отсутствуют права для *входа в систему в качестве службы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="ff20c-233">Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="ff20c-234">Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ff20c-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="ff20c-235">Порт URL-адреса запроса неправильно указан или настроен в приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="ff20c-236">Журналы событий системы и приложений</span><span class="sxs-lookup"><span data-stu-id="ff20c-236">System and Application Event Logs</span></span>

<span data-ttu-id="ff20c-237">Получите доступ к журналам событий системы и приложений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="ff20c-238">Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="ff20c-239">В **средстве просмотра событий** откройте узел **Журналы Windows**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="ff20c-240">Выберите **Система**, чтобы открыть журнал системных событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="ff20c-241">Выберите **Приложение**, чтобы открыть журнал событий приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="ff20c-242">Проверьте здесь наличие ошибок, связанных с проблемным приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="ff20c-243">Запуск приложения в командной строке</span><span class="sxs-lookup"><span data-stu-id="ff20c-243">Run the app at a command prompt</span></span>

<span data-ttu-id="ff20c-244">Многие ошибки запуска не создают полезные сведения в журналах событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="ff20c-245">Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="ff20c-246">Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ff20c-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="ff20c-247">Очистка кэшей пакетов</span><span class="sxs-lookup"><span data-stu-id="ff20c-247">Clear package caches</span></span>

<span data-ttu-id="ff20c-248">Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="ff20c-249">В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="ff20c-250">Большинство этих проблем можно исправить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ff20c-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="ff20c-251">Удалите папки *bin* и *obj*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="ff20c-252">Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="ff20c-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="ff20c-253">Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="ff20c-254">*NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="ff20c-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="ff20c-255">Восстановите и перестройте проект.</span><span class="sxs-lookup"><span data-stu-id="ff20c-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="ff20c-256">Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="ff20c-257">Медленное или зависающее приложение</span><span class="sxs-lookup"><span data-stu-id="ff20c-257">Slow or hanging app</span></span>

<span data-ttu-id="ff20c-258">*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="ff20c-259">Аварийное завершение работы приложения или исключение</span><span class="sxs-lookup"><span data-stu-id="ff20c-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="ff20c-260">Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="ff20c-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="ff20c-261">Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="ff20c-262">Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:</span><span class="sxs-lookup"><span data-stu-id="ff20c-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="ff20c-263">Запустите приложение в условиях, вызывающих аварийное завершение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="ff20c-264">После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="ff20c-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="ff20c-265">Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом.</span><span class="sxs-lookup"><span data-stu-id="ff20c-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="ff20c-266">Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="ff20c-267">Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).</span><span class="sxs-lookup"><span data-stu-id="ff20c-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="ff20c-268">Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме</span><span class="sxs-lookup"><span data-stu-id="ff20c-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="ff20c-269">Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.</span><span class="sxs-lookup"><span data-stu-id="ff20c-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="ff20c-270">Анализ дампа</span><span class="sxs-lookup"><span data-stu-id="ff20c-270">Analyze the dump</span></span>

<span data-ttu-id="ff20c-271">Дамп можно проанализировать несколькими способами.</span><span class="sxs-lookup"><span data-stu-id="ff20c-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="ff20c-272">Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="ff20c-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff20c-273">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ff20c-273">Additional resources</span></span>

* <span data-ttu-id="ff20c-274">[Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)</span><span class="sxs-lookup"><span data-stu-id="ff20c-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ff20c-275">Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="ff20c-276">При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="ff20c-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="ff20c-277">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff20c-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff20c-278">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ff20c-278">Prerequisites</span></span>

* [<span data-ttu-id="ff20c-279">Пакет SDK для ASP.NET Core 2.1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="ff20c-280">PowerShell 6.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="ff20c-281">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="ff20c-281">App configuration</span></span>

<span data-ttu-id="ff20c-282">Приложение требует ссылки на пакеты для [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) и [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="ff20c-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="ff20c-283">Для тестирования и отладки при работе вне службы добавьте код, чтобы определить, как выполняется приложение: в качестве службы или консольного приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="ff20c-284">Проверьте, присоединен ли отладчик или присутствует ли параметр `--console`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="ff20c-285">Если одно из условий имеет значение true (приложение выполняется не в качестве службы), вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="ff20c-286">Если условия имеют значение false (приложение выполняется в качестве службы), сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="ff20c-287">Вызовите <xref:System.IO.Directory.SetCurrentDirectory*> и используйте путь к расположению для публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="ff20c-288">Не вызывайте <xref:System.IO.Directory.GetCurrentDirectory*> для получения пути, так как при вызове <xref:System.IO.Directory.GetCurrentDirectory*> приложение службы Windows возвращает папку *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="ff20c-289">Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="ff20c-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="ff20c-290">Этот шаг выполняется до того, как приложение будет настроено в `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="ff20c-291">Вызовите <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, чтобы запустить приложение в качестве службы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="ff20c-292">Так как [поставщик конфигурации командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) требует указания пар "имя-значение" для аргументов командной строки, параметр `--console` удаляется из аргументов, прежде чем <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> получит их.</span><span class="sxs-lookup"><span data-stu-id="ff20c-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="ff20c-293">Для записи данных в журнал событий Windows добавьте поставщик EventLog в <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="ff20c-294">Задайте уровень ведения журнала с помощью ключа `Logging:LogLevel:Default` в файле *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="ff20c-295">В следующем примере `RunAsCustomService` вызывается вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> для обработки событий времени существования в приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="ff20c-296">Дополнительные сведения см. в разделе [Обработка событий запуска и остановки](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="ff20c-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="ff20c-297">Тип развертывания</span><span class="sxs-lookup"><span data-stu-id="ff20c-297">Deployment type</span></span>

<span data-ttu-id="ff20c-298">Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="ff20c-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="ff20c-299">SDK</span><span class="sxs-lookup"><span data-stu-id="ff20c-299">SDK</span></span>

<span data-ttu-id="ff20c-300">Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="ff20c-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ff20c-301">Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="ff20c-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="ff20c-302">Зависящее от платформы развертывание (FDD)</span><span class="sxs-lookup"><span data-stu-id="ff20c-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="ff20c-303">Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="ff20c-304">Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="ff20c-305">[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) содержит целевую версию платформы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="ff20c-306">В следующем примере для RID задано значение `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="ff20c-307">Свойству `<SelfContained>` задано значение `false`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="ff20c-308">Эти свойства указывают пакету SDK создать исполняемый файл ( *.exe*) для Windows и приложение, которое зависит от общей платформы .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="ff20c-309">Файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="ff20c-310">Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="ff20c-311">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="ff20c-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="ff20c-312">Для автономного развертывания необязательно наличие общей платформы в системе размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="ff20c-313">Среда выполнения и зависимости приложения развертываются с приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="ff20c-314">[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:</span><span class="sxs-lookup"><span data-stu-id="ff20c-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="ff20c-315">Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="ff20c-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="ff20c-316">Укажите список идентификаторов RID, разделив их точкой с запятой.</span><span class="sxs-lookup"><span data-stu-id="ff20c-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="ff20c-317">Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).</span><span class="sxs-lookup"><span data-stu-id="ff20c-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="ff20c-318">Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ff20c-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="ff20c-319">Для свойства `<SelfContained>` задано значение `true`:</span><span class="sxs-lookup"><span data-stu-id="ff20c-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="ff20c-320">Учетная запись пользователя службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-320">Service user account</span></span>

<span data-ttu-id="ff20c-321">Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="ff20c-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="ff20c-322">Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="ff20c-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-323">Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="ff20c-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="ff20c-324">Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.</span><span class="sxs-lookup"><span data-stu-id="ff20c-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="ff20c-325">Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).</span><span class="sxs-lookup"><span data-stu-id="ff20c-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="ff20c-326">Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).</span><span class="sxs-lookup"><span data-stu-id="ff20c-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="ff20c-327">Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб.</span><span class="sxs-lookup"><span data-stu-id="ff20c-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="ff20c-328">Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="ff20c-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="ff20c-329">Права на вход в качестве службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-329">Log on as a service rights</span></span>

<span data-ttu-id="ff20c-330">Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="ff20c-331">Откройте редактор локальной политики безопасности, запустив *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="ff20c-332">Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="ff20c-333">Откройте политику **Вход в качестве службы**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="ff20c-334">Щелкните **Добавить пользователя или группу**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="ff20c-335">Укажите имя объекта (учетная запись пользователя) одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="ff20c-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="ff20c-336">Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="ff20c-337">Выберите **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-337">Select **Advanced**.</span></span> <span data-ttu-id="ff20c-338">Нажмите **Найти**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-338">Select **Find Now**.</span></span> <span data-ttu-id="ff20c-339">Выберите учетную запись пользователя из списка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-339">Select the user account from the list.</span></span> <span data-ttu-id="ff20c-340">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-340">Select **OK**.</span></span> <span data-ttu-id="ff20c-341">Нажмите **ОК** еще раз, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="ff20c-342">Нажмите **ОК** или **Применить**, чтобы сохранить изменения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="ff20c-343">Создание службы Windows и управление ею</span><span class="sxs-lookup"><span data-stu-id="ff20c-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="ff20c-344">Создание службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-344">Create a service</span></span>

<span data-ttu-id="ff20c-345">Зарегистрируйте службу с помощью команды PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="ff20c-346">В административной оболочке PowerShell 6 выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="ff20c-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="ff20c-347">`{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="ff20c-348">Не включайте исполняемый файл приложения в путь.</span><span class="sxs-lookup"><span data-stu-id="ff20c-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="ff20c-349">Завершающая косая черта не требуется.</span><span class="sxs-lookup"><span data-stu-id="ff20c-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="ff20c-350">`{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="ff20c-351">`{SERVICE NAME}`. Имя службы (например, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="ff20c-352">`{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="ff20c-353">Включите имя исполняемого файла с расширением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="ff20c-354">`{DESCRIPTION}`. Описание службы (например, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="ff20c-355">`{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="ff20c-356">Запуск службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-356">Start a service</span></span>

<span data-ttu-id="ff20c-357">Запустите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-358">Команде потребуется несколько секунд, чтобы запустить службу.</span><span class="sxs-lookup"><span data-stu-id="ff20c-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="ff20c-359">Определение состояния службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-359">Determine a service's status</span></span>

<span data-ttu-id="ff20c-360">Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-361">Состояние отображается одним из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="ff20c-362">Остановка службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-362">Stop a service</span></span>

<span data-ttu-id="ff20c-363">Остановите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="ff20c-364">Удаление службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-364">Remove a service</span></span>

<span data-ttu-id="ff20c-365">После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="ff20c-366">Обработка событий запуска и остановки</span><span class="sxs-lookup"><span data-stu-id="ff20c-366">Handle starting and stopping events</span></span>

<span data-ttu-id="ff20c-367">Чтобы обработать события <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> и <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="ff20c-368">Создайте класс, производный от <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>, с методами `OnStarting`, `OnStarted` и `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="ff20c-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="ff20c-369">Создайте метод расширения для <xref:Microsoft.AspNetCore.Hosting.IWebHost>, который передает `CustomWebHostService` в <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="ff20c-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="ff20c-370">В `Program.Main` вызовите метод расширения `RunAsCustomService` вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="ff20c-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="ff20c-371">Чтобы узнать расположение <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> в `Program.Main`, см. пример кода из раздела [Тип развертывания](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="ff20c-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ff20c-372">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="ff20c-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ff20c-373">Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="ff20c-374">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="ff20c-375">Настройка конечных точек</span><span class="sxs-lookup"><span data-stu-id="ff20c-375">Configure endpoints</span></span>

<span data-ttu-id="ff20c-376">По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ff20c-377">Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="ff20c-378">Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:</span><span class="sxs-lookup"><span data-stu-id="ff20c-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="ff20c-379">В предыдущем руководстве рассматривается поддержка конечных точек HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="ff20c-380">Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="ff20c-381">Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ff20c-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="ff20c-382">Текущий каталог и корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="ff20c-382">Current directory and content root</span></span>

<span data-ttu-id="ff20c-383">Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory*> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="ff20c-384">Папка *system32* не подходит для хранения файлов службы (например, файлов параметров).</span><span class="sxs-lookup"><span data-stu-id="ff20c-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="ff20c-385">Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.</span><span class="sxs-lookup"><span data-stu-id="ff20c-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="ff20c-386">Указание папки приложения в качестве пути корневого каталога содержимого</span><span class="sxs-lookup"><span data-stu-id="ff20c-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="ff20c-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> — это тот же путь, который предоставляется аргументу `binPath` при создании службы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="ff20c-388">Чтобы не вызывать метод `GetCurrentDirectory` для создания путей к файлам параметров, вызовите <xref:System.IO.Directory.SetCurrentDirectory*> с указанным путем к [корневому каталогу содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="ff20c-389">В `Program.Main` определите путь к папке с исполняемым файлом службы и используйте этот путь, чтобы создать корневой каталог содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="ff20c-390">Хранение файлов службы в подходящем расположении на диске</span><span class="sxs-lookup"><span data-stu-id="ff20c-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="ff20c-391">Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ff20c-392">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ff20c-392">Troubleshoot</span></span>

<span data-ttu-id="ff20c-393">Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="ff20c-394">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="ff20c-394">Common errors</span></span>

* <span data-ttu-id="ff20c-395">Используется старая или предварительная версия PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="ff20c-396">Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="ff20c-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ff20c-397">Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений.</span><span class="sxs-lookup"><span data-stu-id="ff20c-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="ff20c-398">В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:</span><span class="sxs-lookup"><span data-stu-id="ff20c-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="ff20c-399">*bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);</span><span class="sxs-lookup"><span data-stu-id="ff20c-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="ff20c-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).</span><span class="sxs-lookup"><span data-stu-id="ff20c-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="ff20c-401">Служба не находится в состоянии выполнения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="ff20c-402">Пути к используемым приложением ресурсам (например, к сертификатам) неверные.</span><span class="sxs-lookup"><span data-stu-id="ff20c-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="ff20c-403">Базовый путь к службе Windows — *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="ff20c-404">У пользователя отсутствуют права для *входа в систему в качестве службы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="ff20c-405">Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="ff20c-406">Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ff20c-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="ff20c-407">Порт URL-адреса запроса неправильно указан или настроен в приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="ff20c-408">Журналы событий системы и приложений</span><span class="sxs-lookup"><span data-stu-id="ff20c-408">System and Application Event Logs</span></span>

<span data-ttu-id="ff20c-409">Получите доступ к журналам событий системы и приложений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="ff20c-410">Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="ff20c-411">В **средстве просмотра событий** откройте узел **Журналы Windows**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="ff20c-412">Выберите **Система**, чтобы открыть журнал системных событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="ff20c-413">Выберите **Приложение**, чтобы открыть журнал событий приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="ff20c-414">Проверьте здесь наличие ошибок, связанных с проблемным приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="ff20c-415">Запуск приложения в командной строке</span><span class="sxs-lookup"><span data-stu-id="ff20c-415">Run the app at a command prompt</span></span>

<span data-ttu-id="ff20c-416">Многие ошибки запуска не создают полезные сведения в журналах событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="ff20c-417">Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="ff20c-418">Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ff20c-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="ff20c-419">Очистка кэшей пакетов</span><span class="sxs-lookup"><span data-stu-id="ff20c-419">Clear package caches</span></span>

<span data-ttu-id="ff20c-420">Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="ff20c-421">В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="ff20c-422">Большинство этих проблем можно исправить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ff20c-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="ff20c-423">Удалите папки *bin* и *obj*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="ff20c-424">Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="ff20c-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="ff20c-425">Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="ff20c-426">*NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="ff20c-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="ff20c-427">Восстановите и перестройте проект.</span><span class="sxs-lookup"><span data-stu-id="ff20c-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="ff20c-428">Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="ff20c-429">Медленное или зависающее приложение</span><span class="sxs-lookup"><span data-stu-id="ff20c-429">Slow or hanging app</span></span>

<span data-ttu-id="ff20c-430">*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="ff20c-431">Аварийное завершение работы приложения или исключение</span><span class="sxs-lookup"><span data-stu-id="ff20c-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="ff20c-432">Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="ff20c-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="ff20c-433">Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="ff20c-434">Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:</span><span class="sxs-lookup"><span data-stu-id="ff20c-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="ff20c-435">Запустите приложение в условиях, вызывающих аварийное завершение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="ff20c-436">После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="ff20c-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="ff20c-437">Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом.</span><span class="sxs-lookup"><span data-stu-id="ff20c-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="ff20c-438">Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="ff20c-439">Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).</span><span class="sxs-lookup"><span data-stu-id="ff20c-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="ff20c-440">Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме</span><span class="sxs-lookup"><span data-stu-id="ff20c-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="ff20c-441">Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.</span><span class="sxs-lookup"><span data-stu-id="ff20c-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="ff20c-442">Анализ дампа</span><span class="sxs-lookup"><span data-stu-id="ff20c-442">Analyze the dump</span></span>

<span data-ttu-id="ff20c-443">Дамп можно проанализировать несколькими способами.</span><span class="sxs-lookup"><span data-stu-id="ff20c-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="ff20c-444">Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="ff20c-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff20c-445">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ff20c-445">Additional resources</span></span>

* <span data-ttu-id="ff20c-446">[Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)</span><span class="sxs-lookup"><span data-stu-id="ff20c-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ff20c-447">Приложение ASP.NET Core можно разместить в Windows в качестве [службы Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) без использования IIS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="ff20c-448">При размещении в качестве службы Windows приложение автоматически запускается после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="ff20c-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="ff20c-449">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff20c-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff20c-450">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ff20c-450">Prerequisites</span></span>

* [<span data-ttu-id="ff20c-451">Пакет SDK для ASP.NET Core 2.1 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="ff20c-452">PowerShell 6.2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="ff20c-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="ff20c-453">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="ff20c-453">App configuration</span></span>

<span data-ttu-id="ff20c-454">Приложение требует ссылки на пакеты для [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) и [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="ff20c-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="ff20c-455">Для тестирования и отладки при работе вне службы добавьте код, чтобы определить, как выполняется приложение: в качестве службы или консольного приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="ff20c-456">Проверьте, присоединен ли отладчик или присутствует ли параметр `--console`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="ff20c-457">Если одно из условий имеет значение true (приложение выполняется не в качестве службы), вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="ff20c-458">Если условия имеют значение false (приложение выполняется в качестве службы), сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="ff20c-459">Вызовите <xref:System.IO.Directory.SetCurrentDirectory*> и используйте путь к расположению для публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="ff20c-460">Не вызывайте <xref:System.IO.Directory.GetCurrentDirectory*> для получения пути, так как при вызове <xref:System.IO.Directory.GetCurrentDirectory*> приложение службы Windows возвращает папку *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="ff20c-461">Дополнительные сведения см. в разделе [Текущий каталог и корневой каталог содержимого](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="ff20c-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="ff20c-462">Этот шаг выполняется до того, как приложение будет настроено в `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="ff20c-463">Вызовите <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>, чтобы запустить приложение в качестве службы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="ff20c-464">Так как [поставщик конфигурации командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) требует указания пар "имя-значение" для аргументов командной строки, параметр `--console` удаляется из аргументов, прежде чем <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> получит их.</span><span class="sxs-lookup"><span data-stu-id="ff20c-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="ff20c-465">Для записи данных в журнал событий Windows добавьте поставщик EventLog в <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="ff20c-466">Задайте уровень ведения журнала с помощью ключа `Logging:LogLevel:Default` в файле *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="ff20c-467">В следующем примере `RunAsCustomService` вызывается вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> для обработки событий времени существования в приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="ff20c-468">Дополнительные сведения см. в разделе [Обработка событий запуска и остановки](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="ff20c-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="ff20c-469">Тип развертывания</span><span class="sxs-lookup"><span data-stu-id="ff20c-469">Deployment type</span></span>

<span data-ttu-id="ff20c-470">Дополнительные сведения и рекомендации по сценариям развертывания см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="ff20c-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="ff20c-471">SDK</span><span class="sxs-lookup"><span data-stu-id="ff20c-471">SDK</span></span>

<span data-ttu-id="ff20c-472">Для службы на основе веб-приложений, которая использует платформы MVC или Razor Pages, укажите веб-пакет SDK в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="ff20c-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="ff20c-473">Если служба выполняет только фоновые задачи (например, [размещенные службы](xref:fundamentals/host/hosted-services)), укажите пакет SDK рабочей роли в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="ff20c-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="ff20c-474">Зависящее от платформы развертывание (FDD)</span><span class="sxs-lookup"><span data-stu-id="ff20c-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="ff20c-475">Зависящее от платформы развертывание (FDD) требует наличия в целевой системе общей для всей системы версии .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="ff20c-476">Если сценарий с FDD используется согласно инструкций в этой статье, пакет SDK создаcт исполняемый файл ( *.exe*), который называется *исполняемым файлом, зависящим от платформы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="ff20c-477">[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) содержит целевую версию платформы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="ff20c-478">В следующем примере для RID задано значение `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="ff20c-479">Свойству `<SelfContained>` задано значение `false`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="ff20c-480">Эти свойства указывают пакету SDK создать исполняемый файл ( *.exe*) для Windows и приложение, которое зависит от общей платформы .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff20c-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="ff20c-481">Свойству `<UseAppHost>` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="ff20c-482">Это свойство предоставляет службу с путем активации (исполняемый файл, *EXE*) для FDD.</span><span class="sxs-lookup"><span data-stu-id="ff20c-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="ff20c-483">Файл *web.config*, который обычно создается при публикации приложения ASP.NET Core, не требуется для приложения служб Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="ff20c-484">Отмените создание файла *web.config*, добавив свойство `<IsTransformWebConfigDisabled>` со значением `true`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="ff20c-485">Автономное развертывание</span><span class="sxs-lookup"><span data-stu-id="ff20c-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="ff20c-486">Для автономного развертывания необязательно наличие общей платформы в системе размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="ff20c-487">Среда выполнения и зависимости приложения развертываются с приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="ff20c-488">[Идентификатор среды выполнения](/dotnet/core/rid-catalog) Windows включается в `<PropertyGroup>`, где содержится целевая платформа:</span><span class="sxs-lookup"><span data-stu-id="ff20c-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="ff20c-489">Чтобы выполнить публикацию для нескольких идентификаторов RID, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="ff20c-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="ff20c-490">Укажите список идентификаторов RID, разделив их точкой с запятой.</span><span class="sxs-lookup"><span data-stu-id="ff20c-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="ff20c-491">Укажите имя свойства [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (множественное число).</span><span class="sxs-lookup"><span data-stu-id="ff20c-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="ff20c-492">Дополнительные сведения см. в [каталоге RID для .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="ff20c-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="ff20c-493">Для свойства `<SelfContained>` задано значение `true`:</span><span class="sxs-lookup"><span data-stu-id="ff20c-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="ff20c-494">Учетная запись пользователя службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-494">Service user account</span></span>

<span data-ttu-id="ff20c-495">Создайте для службы учетную запись пользователя с помощью командлета [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) в административной оболочке PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="ff20c-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="ff20c-496">Обновление Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763) или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="ff20c-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-497">Версия Windows, предшествующая обновлению Windows 10 за октябрь 2018 г. (версия 1809, сборка 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="ff20c-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="ff20c-498">Укажите [надежный пароль](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) при появлении соответствующего запроса.</span><span class="sxs-lookup"><span data-stu-id="ff20c-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="ff20c-499">Параметр срока действия учетной записи <xref:System.DateTime> можно задать с помощью параметра `-AccountExpires`, передаваемого в командлет [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser).</span><span class="sxs-lookup"><span data-stu-id="ff20c-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="ff20c-500">Дополнительные сведения см. в статьях [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) и [Service User Accounts](/windows/desktop/services/service-user-accounts) (Учетные записи пользователей служб).</span><span class="sxs-lookup"><span data-stu-id="ff20c-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="ff20c-501">Альтернативный подход к управлению пользователями при работе с Active Directory заключается в применении управляемых учетных записей служб.</span><span class="sxs-lookup"><span data-stu-id="ff20c-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="ff20c-502">Дополнительные сведения см. в [обзоре групповых управляемых учетных записей службы](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="ff20c-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="ff20c-503">Права на вход в качестве службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-503">Log on as a service rights</span></span>

<span data-ttu-id="ff20c-504">Чтобы настроить право *Вход в качестве службы* для учетной записи пользователя службы, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="ff20c-505">Откройте редактор локальной политики безопасности, запустив *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="ff20c-506">Разверните узел **Локальные политики** и выберите **Назначение прав пользователя**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="ff20c-507">Откройте политику **Вход в качестве службы**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="ff20c-508">Щелкните **Добавить пользователя или группу**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="ff20c-509">Укажите имя объекта (учетная запись пользователя) одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="ff20c-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="ff20c-510">Укажите учетную запись пользователя (`{DOMAIN OR COMPUTER NAME\USER}`) в поле для имени объекта и нажмите **ОК**, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="ff20c-511">Выберите **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-511">Select **Advanced**.</span></span> <span data-ttu-id="ff20c-512">Нажмите **Найти**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-512">Select **Find Now**.</span></span> <span data-ttu-id="ff20c-513">Выберите учетную запись пользователя из списка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-513">Select the user account from the list.</span></span> <span data-ttu-id="ff20c-514">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-514">Select **OK**.</span></span> <span data-ttu-id="ff20c-515">Нажмите **ОК** еще раз, чтобы назначить политику пользователю.</span><span class="sxs-lookup"><span data-stu-id="ff20c-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="ff20c-516">Нажмите **ОК** или **Применить**, чтобы сохранить изменения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="ff20c-517">Создание службы Windows и управление ею</span><span class="sxs-lookup"><span data-stu-id="ff20c-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="ff20c-518">Создание службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-518">Create a service</span></span>

<span data-ttu-id="ff20c-519">Зарегистрируйте службу с помощью команды PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="ff20c-520">В административной оболочке PowerShell 6 выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="ff20c-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="ff20c-521">`{EXE PATH}`. Путь к папке приложения на узле (например, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="ff20c-522">Не включайте исполняемый файл приложения в путь.</span><span class="sxs-lookup"><span data-stu-id="ff20c-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="ff20c-523">Завершающая косая черта не требуется.</span><span class="sxs-lookup"><span data-stu-id="ff20c-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="ff20c-524">`{DOMAIN OR COMPUTER NAME\USER}`. Учетная запись пользователя службы (например, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="ff20c-525">`{SERVICE NAME}`. Имя службы (например, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="ff20c-526">`{EXE FILE PATH}`. Путь к исполняемому файлу приложения (например, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="ff20c-527">Включите имя исполняемого файла с расширением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="ff20c-528">`{DESCRIPTION}`. Описание службы (например, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="ff20c-529">`{DISPLAY NAME}`. Отображаемое имя службы (например, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="ff20c-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="ff20c-530">Запуск службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-530">Start a service</span></span>

<span data-ttu-id="ff20c-531">Запустите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-532">Команде потребуется несколько секунд, чтобы запустить службу.</span><span class="sxs-lookup"><span data-stu-id="ff20c-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="ff20c-533">Определение состояния службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-533">Determine a service's status</span></span>

<span data-ttu-id="ff20c-534">Чтобы проверить состояние службы, используйте следующую команду PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="ff20c-535">Состояние отображается одним из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="ff20c-536">Остановка службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-536">Stop a service</span></span>

<span data-ttu-id="ff20c-537">Остановите службу с помощью следующей команды PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="ff20c-538">Удаление службы</span><span class="sxs-lookup"><span data-stu-id="ff20c-538">Remove a service</span></span>

<span data-ttu-id="ff20c-539">После небольшой задержки для остановки службы удалите службу с помощью следующей команды Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="ff20c-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="ff20c-540">Обработка событий запуска и остановки</span><span class="sxs-lookup"><span data-stu-id="ff20c-540">Handle starting and stopping events</span></span>

<span data-ttu-id="ff20c-541">Чтобы обработать события <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> и <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ff20c-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="ff20c-542">Создайте класс, производный от <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>, с методами `OnStarting`, `OnStarted` и `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="ff20c-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="ff20c-543">Создайте метод расширения для <xref:Microsoft.AspNetCore.Hosting.IWebHost>, который передает `CustomWebHostService` в <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="ff20c-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="ff20c-544">В `Program.Main` вызовите метод расширения `RunAsCustomService` вместо <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="ff20c-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="ff20c-545">Чтобы узнать расположение <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> в `Program.Main`, см. пример кода из раздела [Тип развертывания](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="ff20c-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ff20c-546">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="ff20c-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ff20c-547">Для служб, которые взаимодействуют с запросами из Интернета или корпоративной сети и размещаются за прокси-сервером или подсистемой балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="ff20c-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="ff20c-548">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="ff20c-549">Настройка конечных точек</span><span class="sxs-lookup"><span data-stu-id="ff20c-549">Configure endpoints</span></span>

<span data-ttu-id="ff20c-550">По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ff20c-551">Настройте URL-адрес и порт, задав переменную среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="ff20c-552">Дополнительные сведения о подходах к настройке URL-адресов и портов см. в соответствующей статье сервера:</span><span class="sxs-lookup"><span data-stu-id="ff20c-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="ff20c-553">В предыдущем руководстве рассматривается поддержка конечных точек HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ff20c-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="ff20c-554">Например, настройте приложение для HTTPS, если проверка подлинности используется со службой Windows.</span><span class="sxs-lookup"><span data-stu-id="ff20c-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="ff20c-555">Использование сертификата разработки ASP.NET Core HTTPS для защиты конечной точки службы не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ff20c-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="ff20c-556">Текущий каталог и корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="ff20c-556">Current directory and content root</span></span>

<span data-ttu-id="ff20c-557">Для службы Windows <xref:System.IO.Directory.GetCurrentDirectory*> возвращает текущий рабочий каталог *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="ff20c-558">Папка *system32* не подходит для хранения файлов службы (например, файлов параметров).</span><span class="sxs-lookup"><span data-stu-id="ff20c-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="ff20c-559">Используйте один из следующих методов для сохранения ресурсов и файлов параметров службы и доступа к ним.</span><span class="sxs-lookup"><span data-stu-id="ff20c-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="ff20c-560">Указание папки приложения в качестве пути корневого каталога содержимого</span><span class="sxs-lookup"><span data-stu-id="ff20c-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="ff20c-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> — это тот же путь, который предоставляется аргументу `binPath` при создании службы.</span><span class="sxs-lookup"><span data-stu-id="ff20c-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="ff20c-562">Чтобы не вызывать метод `GetCurrentDirectory` для создания путей к файлам параметров, вызовите <xref:System.IO.Directory.SetCurrentDirectory*> с указанным путем к [корневому каталогу содержимого](xref:fundamentals/index#content-root) приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="ff20c-563">В `Program.Main` определите путь к папке с исполняемым файлом службы и используйте этот путь, чтобы создать корневой каталог содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="ff20c-564">Хранение файлов службы в подходящем расположении на диске</span><span class="sxs-lookup"><span data-stu-id="ff20c-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="ff20c-565">Укажите абсолютный путь к папке, содержащей файлы, с помощью <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> при использовании <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ff20c-566">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="ff20c-566">Troubleshoot</span></span>

<span data-ttu-id="ff20c-567">Сведения об устранении неполадок в приложении службы Windows см. в статье <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="ff20c-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="ff20c-568">Распространенные ошибки</span><span class="sxs-lookup"><span data-stu-id="ff20c-568">Common errors</span></span>

* <span data-ttu-id="ff20c-569">Используется старая или предварительная версия PowerShell.</span><span class="sxs-lookup"><span data-stu-id="ff20c-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="ff20c-570">Зарегистрированная служба не использует **опубликованные** выходные данные приложения, возвращенные командой [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="ff20c-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ff20c-571">Выходные данные команды [dotnet build](/dotnet/core/tools/dotnet-build) не поддерживаются для развертывания приложений.</span><span class="sxs-lookup"><span data-stu-id="ff20c-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="ff20c-572">В зависимости от типа развертывания опубликованные ресурсы находятся в одной из следующих папок:</span><span class="sxs-lookup"><span data-stu-id="ff20c-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="ff20c-573">*bin/Release/{TARGET FRAMEWORK}/publish* (зависящее от платформы развертывание);</span><span class="sxs-lookup"><span data-stu-id="ff20c-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="ff20c-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (автономное развертывание).</span><span class="sxs-lookup"><span data-stu-id="ff20c-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="ff20c-575">Служба не находится в состоянии выполнения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="ff20c-576">Пути к используемым приложением ресурсам (например, к сертификатам) неверные.</span><span class="sxs-lookup"><span data-stu-id="ff20c-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="ff20c-577">Базовый путь к службе Windows — *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="ff20c-578">У пользователя отсутствуют права для *входа в систему в качестве службы*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="ff20c-579">Пароль был указан неверно или его срок действия истек при выполнении команды PowerShell `New-Service`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="ff20c-580">Для приложения требуется выполнить проверку подлинности в ASP.NET Core, однако оно не настроено для безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ff20c-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="ff20c-581">Порт URL-адреса запроса неправильно указан или настроен в приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="ff20c-582">Журналы событий системы и приложений</span><span class="sxs-lookup"><span data-stu-id="ff20c-582">System and Application Event Logs</span></span>

<span data-ttu-id="ff20c-583">Получите доступ к журналам событий системы и приложений:</span><span class="sxs-lookup"><span data-stu-id="ff20c-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="ff20c-584">Откройте меню "Пуск", выполните поиск по фразе *Просмотр событий* и выберите приложение **Просмотр событий**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="ff20c-585">В **средстве просмотра событий** откройте узел **Журналы Windows**.</span><span class="sxs-lookup"><span data-stu-id="ff20c-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="ff20c-586">Выберите **Система**, чтобы открыть журнал системных событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="ff20c-587">Выберите **Приложение**, чтобы открыть журнал событий приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="ff20c-588">Проверьте здесь наличие ошибок, связанных с проблемным приложением.</span><span class="sxs-lookup"><span data-stu-id="ff20c-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="ff20c-589">Запуск приложения в командной строке</span><span class="sxs-lookup"><span data-stu-id="ff20c-589">Run the app at a command prompt</span></span>

<span data-ttu-id="ff20c-590">Многие ошибки запуска не создают полезные сведения в журналах событий.</span><span class="sxs-lookup"><span data-stu-id="ff20c-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="ff20c-591">Для некоторых ошибок можно найти причину, запустив приложение в командной строке на компьютере размещения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="ff20c-592">Чтобы записать дополнительные сведения из приложения, снизьте [уровень ведения журнала](xref:fundamentals/logging/index#log-level) или запустите приложение в [среде разработки](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="ff20c-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="ff20c-593">Очистка кэшей пакетов</span><span class="sxs-lookup"><span data-stu-id="ff20c-593">Clear package caches</span></span>

<span data-ttu-id="ff20c-594">Приложения-функции могут перестать работать сразу после обновления пакета SDK для .NET Core на компьютере разработки или обновления версии пакетов в самом приложении.</span><span class="sxs-lookup"><span data-stu-id="ff20c-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="ff20c-595">В некоторых случаях в результате важного обновления несогласованные версии пакетов могут привести к нарушению работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="ff20c-596">Большинство этих проблем можно исправить следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ff20c-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="ff20c-597">Удалите папки *bin* и *obj*.</span><span class="sxs-lookup"><span data-stu-id="ff20c-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="ff20c-598">Очистите кэши пакетов, выполнив команду [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) из командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="ff20c-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="ff20c-599">Очистку кэшей пакетов также можно выполнить с помощью средства [nuget.exe](https://www.nuget.org/downloads) или команды `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="ff20c-600">*NuGet.exe* не входит в пакет установки операционной системы Windows для настольных компьютеров и его нужно получить отдельно на [веб-сайте NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="ff20c-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="ff20c-601">Восстановите и перестройте проект.</span><span class="sxs-lookup"><span data-stu-id="ff20c-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="ff20c-602">Удалите все файлы из папки развертывания на сервере, прежде чем повторно развернуть приложение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="ff20c-603">Медленное или зависающее приложение</span><span class="sxs-lookup"><span data-stu-id="ff20c-603">Slow or hanging app</span></span>

<span data-ttu-id="ff20c-604">*Аварийный дамп* — это моментальный снимок системной памяти, который может помочь определить причину аварийного завершения, сбоя запуска или медленной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="ff20c-605">Аварийное завершение работы приложения или исключение</span><span class="sxs-lookup"><span data-stu-id="ff20c-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="ff20c-606">Получите и проанализируйте дамп из [отчетов об ошибках Windows (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="ff20c-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="ff20c-607">Создайте папку для хранения файлов аварийного дампа в `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="ff20c-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="ff20c-608">Запустите [скрипт PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) с именем исполняемого файла приложения:</span><span class="sxs-lookup"><span data-stu-id="ff20c-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="ff20c-609">Запустите приложение в условиях, вызывающих аварийное завершение.</span><span class="sxs-lookup"><span data-stu-id="ff20c-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="ff20c-610">После аварийного завершения запустите [скрипт PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="ff20c-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="ff20c-611">Когда приложение аварийно завершит работу и сбор дампов будет выполнен, приложение сможет завершить работу обычным образом.</span><span class="sxs-lookup"><span data-stu-id="ff20c-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="ff20c-612">Скрипт PowerShell настраивает отчеты об ошибках Windows для сбора до пяти дампов для приложения.</span><span class="sxs-lookup"><span data-stu-id="ff20c-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="ff20c-613">Аварийные дампы могут занимать много места на диске (до нескольких гигабайтов каждый).</span><span class="sxs-lookup"><span data-stu-id="ff20c-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="ff20c-614">Приложение перестает отвечать на запросы, не запускается или работает в обычном режиме</span><span class="sxs-lookup"><span data-stu-id="ff20c-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="ff20c-615">Когда приложение *перестает отвечать на запросы* (но аварийное завершение не происходит), не запускается или работает в обычном режиме, см. раздел [Файлы дампа пользовательского режима: выбор лучшего инструмента](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), чтобы выбрать подходящий инструмент для создания дампа.</span><span class="sxs-lookup"><span data-stu-id="ff20c-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="ff20c-616">Анализ дампа</span><span class="sxs-lookup"><span data-stu-id="ff20c-616">Analyze the dump</span></span>

<span data-ttu-id="ff20c-617">Дамп можно проанализировать несколькими способами.</span><span class="sxs-lookup"><span data-stu-id="ff20c-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="ff20c-618">Дополнительные сведения см. в разделе [Анализ файла дампа пользовательского режима](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="ff20c-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff20c-619">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ff20c-619">Additional resources</span></span>

* <span data-ttu-id="ff20c-620">[Конфигурация конечных точек Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (включает конфигурацию HTTPS и поддержку SNI)</span><span class="sxs-lookup"><span data-stu-id="ff20c-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
