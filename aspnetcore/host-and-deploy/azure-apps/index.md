---
title: Развертывание приложений ASP.NET Core в Службе приложений Azure
author: bradygaster
description: Эта статья содержит ссылки на ресурсы по размещению и развертыванию в Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
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
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 11de6b04f6813161e5eaee294f3e67e223ae0db3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015924"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="f048a-103">Развертывание приложений ASP.NET Core в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="f048a-104">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) — это [платформа облачных вычислений Microsoft](https://azure.microsoft.com/), предназначенная для размещения веб-приложений, включая ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="f048a-105">Полезные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f048a-105">Useful resources</span></span>

<span data-ttu-id="f048a-106">[Документация по службе приложений](/azure/app-service/) — это место, где хранятся документация, учебники, примеры, руководства и другие ресурсы, связанные с приложениями Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="f048a-107">Размещению приложений ASP.NET Core посвящены следующие два руководства.</span><span class="sxs-lookup"><span data-stu-id="f048a-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="f048a-108">Создание веб-приложения ASP.NET Core в Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="f048a-109">Создайте веб-приложение ASP.NET Core и разверните его в службе приложений Azure на базе Windows с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f048a-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="f048a-110">Создание приложения ASP.NET Core в Службе приложений в Linux</span><span class="sxs-lookup"><span data-stu-id="f048a-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="f048a-111">Создайте веб-приложение ASP.NET Core и разверните его в службе приложений Azure на базе Linux с помощью командной строки.</span><span class="sxs-lookup"><span data-stu-id="f048a-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="f048a-112">Версию ASP.NET Core, доступную в службе приложений, см. в разделе [ASP.NET Core на панели мониторинга службы приложений](https://aspnetcoreon.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="f048a-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="f048a-113">Подпишитесь на репозиторий [объявлений о Службе приложений](https://github.com/Azure/app-service-announcements/) и следите за объявлениями о проблемах.</span><span class="sxs-lookup"><span data-stu-id="f048a-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="f048a-114">Команда Службы приложений регулярно публикует объявления и сценарии, которые готовятся к выпуску в Службе приложений.</span><span class="sxs-lookup"><span data-stu-id="f048a-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="f048a-115">Следующие статьи входят в документацию по ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="f048a-116">Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f048a-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="f048a-117">Сведения о создании веб-приложения ASP.NET Core с помощью Visual Studio и его развертывании в службе приложений Azure с использованием Git для непрерывного развертывания.</span><span class="sxs-lookup"><span data-stu-id="f048a-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="f048a-118">Создание первого конвейера</span><span class="sxs-lookup"><span data-stu-id="f048a-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="f048a-119">Сведения о настройке сборки CI для приложения ASP.NET Core и последующем создании выпуска непрерывного развертывания в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="f048a-120">Песочница веб-приложений Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="f048a-121">Сведения об ограничениях среды выполнения службы приложений Azure, создаваемых платформой приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="f048a-122">Устранение неполадок при возникновении ошибок и предупреждений в проектах ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f048a-123">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="f048a-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="f048a-124">Платформа</span><span class="sxs-lookup"><span data-stu-id="f048a-124">Platform</span></span>

<span data-ttu-id="f048a-125">Если приложение Службы приложений размещено в среде вычислений серии A (цен. категории "Базовый") или на более высоком уровне, архитектура платформы приложения (x86 или x64) задается в его параметрах на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="f048a-126">Убедитесь, что параметры публикации приложения, например в [профиле публикации Visual Studio (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles), соответствуют параметрам в конфигурации службы приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f048a-127">Среды выполнения для 64-разрядных (x64) и 32-разрядных (x86) приложений находятся в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="f048a-128">[Пакет SDK для .NET Core](/dotnet/core/sdk), доступный в Службе приложений, является 32-разрядным, но вы можете развертывать созданные локально 64-разрядные приложения с помощью консоли [Kudu](https://github.com/projectkudu/kudu/wiki) или процесса публикации в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f048a-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="f048a-129">Дополнительные сведения см. в разделе [Публикация и развертывание приложения](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="f048a-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f048a-130">Среды выполнения для 32-разрядных (x86) приложений с собственными зависимостями находятся в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="f048a-131">[Пакет SDK для .NET Core](/dotnet/core/sdk), доступный в Службе приложений, является 32-разрядным.</span><span class="sxs-lookup"><span data-stu-id="f048a-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="f048a-132">Дополнительные сведения о компонентах платформы .NET Core и методах распространения, например сведения о среде выполнения .NET Core и пакете SDK для .NET Core, см. в разделе ["Композиция" статьи сведений о .NET Core](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="f048a-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="f048a-133">Пакеты</span><span class="sxs-lookup"><span data-stu-id="f048a-133">Packages</span></span>

<span data-ttu-id="f048a-134">Включите следующие пакеты NuGet, чтобы использовать возможности автоматического ведения журнала для приложений, развернутых в Службе приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="f048a-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="f048a-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) использует [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) для интеграции подсветки ASP.NET Core в службу приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="f048a-136">Пакет `Microsoft.AspNetCore.AzureAppServicesIntegration` включает дополнительные функции для ведения журналов.</span><span class="sxs-lookup"><span data-stu-id="f048a-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="f048a-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) выполняет [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) для добавления поставщиков ведения журналов диагностики из службы приложений Azure в пакет `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="f048a-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="f048a-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) предоставляет реализации средства ведения журналов в поддержку журналов диагностики службы приложений Azure и функций потоковой передачи журналов.</span><span class="sxs-lookup"><span data-stu-id="f048a-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="f048a-139">Предыдущие пакеты недоступны в [метапакете Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f048a-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f048a-140">Приложения, предназначенные для .NET Framework или ссылающиеся на метапакет `Microsoft.AspNetCore.App`, должны явно ссылаться на отдельные пакеты в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="f048a-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="f048a-141">Переопределение конфигурации приложения с помощью портала Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-141">Override app configuration using the Azure Portal</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f048a-142">Параметры приложений на портале Azure позволяют задать переменные среды для приложения.</span><span class="sxs-lookup"><span data-stu-id="f048a-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="f048a-143">Переменные среды могут использоваться [поставщиком конфигураций переменных среды](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="f048a-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

<span data-ttu-id="f048a-144">Когда вы создаете или изменяете параметр приложения на портале Azure, при нажатии кнопки **Сохранить** происходит перезапуск приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="f048a-145">Переменная среды доступна в приложении после перезапуска службы.</span><span class="sxs-lookup"><span data-stu-id="f048a-145">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="f048a-146">Если приложение использует [универсальный узел](xref:fundamentals/host/generic-host), переменные среды загружаются в конфигурацию приложения, когда метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> вызывается для создания узла.</span><span class="sxs-lookup"><span data-stu-id="f048a-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="f048a-147">Дополнительные сведения см. в разделах <xref:fundamentals/host/generic-host> и [Конфигурация для разных сред](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="f048a-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f048a-148">Параметры приложений на портале Azure позволяют задать переменные среды для приложения.</span><span class="sxs-lookup"><span data-stu-id="f048a-148">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="f048a-149">Переменные среды могут использоваться [поставщиком конфигураций переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f048a-149">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="f048a-150">Когда вы создаете или изменяете параметр приложения на портале Azure, при нажатии кнопки **Сохранить** происходит перезапуск приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-150">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="f048a-151">Переменная среды доступна в приложении после перезапуска службы.</span><span class="sxs-lookup"><span data-stu-id="f048a-151">The environment variable is available to the app after the service restarts.</span></span>

<span data-ttu-id="f048a-152">Если приложение использует [веб-узел](xref:fundamentals/host/web-host), переменные среды загружаются в конфигурацию приложения, когда метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> вызывается для создания узла.</span><span class="sxs-lookup"><span data-stu-id="f048a-152">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="f048a-153">Дополнительные сведения см. в разделах <xref:fundamentals/host/web-host> и [Конфигурация для разных сред](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f048a-153">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f048a-154">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="f048a-154">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f048a-155">[ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), которое настраивает ПО промежуточного слоя переадресации заголовков при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), и модуль ASP.NET Core настраиваются на пересылку схемы (HTTP/HTTPS) и удаленного IP-адреса расположения, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="f048a-155">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="f048a-156">Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="f048a-156">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="f048a-157">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="f048a-157">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="f048a-158">Мониторинг и ведение журналов</span><span class="sxs-lookup"><span data-stu-id="f048a-158">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f048a-159">Приложения ASP.NET Core, развернутые в Службе приложений, автоматически получают расширение Службы приложений для **интеграции ведения журналов ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f048a-159">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="f048a-160">Это расширение обеспечивает интеграцию ведения журналов для приложений ASP.NET Core, развернутых в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-160">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f048a-161">Приложения ASP.NET Core, развернутые в Службе приложений автоматически, получают расширение службы приложений и расширения для **ведения журналов ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f048a-161">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="f048a-162">Это расширение обеспечивает интеграцию ведения журналов для приложений ASP.NET Core, развернутых в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-162">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="f048a-163">Сведения о мониторинге, ведении журналов, а также поиске и устранении неполадок см. в следующих статьях.</span><span class="sxs-lookup"><span data-stu-id="f048a-163">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="f048a-164">Мониторинг приложений в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-164">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="f048a-165">Сведения о том, как толковать квоты и параметры для приложений и планы службы приложений.</span><span class="sxs-lookup"><span data-stu-id="f048a-165">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="f048a-166">Включение функции ведения журналов диагностики для приложений в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-166">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="f048a-167">Сведения о том, как включать и где искать функцию ведения журнала диагностики для кодов статуса HTTP, невыполненных запросов и активности веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="f048a-167">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="f048a-168">Сведения об основных методах обработки ошибок в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-168">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="f048a-169">Сведения о диагностике проблем с развертыванием приложений ASP.NET Core в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-169">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="f048a-170">См. распространенные ошибки с конфигурацией развертывания для приложений, размещенных в службе приложений Azure/IIS, и рекомендации по их устранению.</span><span class="sxs-lookup"><span data-stu-id="f048a-170">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="f048a-171">Связка ключей для защиты данных и слоты развертывания</span><span class="sxs-lookup"><span data-stu-id="f048a-171">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="f048a-172">[Ключи для защиты данных](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) хранятся в папке *%HOME%\ASP.NET\DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="f048a-172">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="f048a-173">Эта папка копируется в сетевое хранилище и синхронизируется на всех машинах, где размещается приложение.</span><span class="sxs-lookup"><span data-stu-id="f048a-173">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="f048a-174">Во время хранения ключи не защищаются.</span><span class="sxs-lookup"><span data-stu-id="f048a-174">Keys aren't protected at rest.</span></span> <span data-ttu-id="f048a-175">В этой папке хранится связка ключей для всех экземпляров приложения в одном и том же слоте развертывания.</span><span class="sxs-lookup"><span data-stu-id="f048a-175">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="f048a-176">Отдельные слоты развертывания, такие как промежуточное хранение и производство, не используют общую связку ключей.</span><span class="sxs-lookup"><span data-stu-id="f048a-176">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="f048a-177">При переключении между разными слотами развертывания ни одна система с использованием защиты данных не сможет расшифровать хранимые данные, используя связку ключей из предыдущего слота.</span><span class="sxs-lookup"><span data-stu-id="f048a-177">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="f048a-178">Для защиты файлов cookie ПО промежуточного слоя ASP.NET Cookie использует защиту данных.</span><span class="sxs-lookup"><span data-stu-id="f048a-178">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="f048a-179">В результате пользователей, применяющих ПО промежуточного слоя ASP.NET Cookie, выбрасывает из приложения.</span><span class="sxs-lookup"><span data-stu-id="f048a-179">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="f048a-180">Для того чтобы решение связки ключей не зависело от слота, используйте внешнего поставщика связки ключей, например:</span><span class="sxs-lookup"><span data-stu-id="f048a-180">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="f048a-181">Хранилище больших двоичных объектов Azure;</span><span class="sxs-lookup"><span data-stu-id="f048a-181">Azure Blob Storage</span></span>
* <span data-ttu-id="f048a-182">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="f048a-182">Azure Key Vault</span></span>
* <span data-ttu-id="f048a-183">Хранилище SQL;</span><span class="sxs-lookup"><span data-stu-id="f048a-183">SQL store</span></span>
* <span data-ttu-id="f048a-184">Кэш Redis.</span><span class="sxs-lookup"><span data-stu-id="f048a-184">Redis cache</span></span>

<span data-ttu-id="f048a-185">Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="f048a-185">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="f048a-186">Развертывание приложения ASP.NET Core, использующего предварительную версию .NET Core</span><span class="sxs-lookup"><span data-stu-id="f048a-186">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="f048a-187">Сведения о развертывании приложения, использующего предварительную версию .NET Core, см. в следующих ресурсах.</span><span class="sxs-lookup"><span data-stu-id="f048a-187">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="f048a-188">Эти способы также используются в случаях, когда среда выполнения доступна, но пакет SDK не установлен в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-188">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* <span data-ttu-id="f048a-189">[Указание версии пакета SDK для .NET Core с помощью Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines).</span><span class="sxs-lookup"><span data-stu-id="f048a-189">[Specify the .NET Core SDK Version using Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)</span></span>
* [<span data-ttu-id="f048a-190">Развертывание автономного приложения для предварительной версии</span><span class="sxs-lookup"><span data-stu-id="f048a-190">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="f048a-191">Использование Docker с веб-приложениями для контейнеров</span><span class="sxs-lookup"><span data-stu-id="f048a-191">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="f048a-192">Установка расширения сайта предварительной версии</span><span class="sxs-lookup"><span data-stu-id="f048a-192">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="f048a-193">Версию ASP.NET Core, доступную в службе приложений, см. в разделе [ASP.NET Core на панели мониторинга службы приложений](https://aspnetcoreon.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="f048a-193">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="f048a-194">Указание версии пакета SDK для .NET Core с помощью Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f048a-194">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="f048a-195">Используйте [сценарии CI/CD Службы приложений Azure](/azure/app-service/deploy-continuous-deployment), чтобы настроить сборку с непрерывной интеграцией с помощью Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f048a-195">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="f048a-196">Создав сборку Azure DevOps, при необходимости настройте ее для использования определенной версии пакета SDK.</span><span class="sxs-lookup"><span data-stu-id="f048a-196">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="f048a-197">Указание версии пакета SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="f048a-197">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="f048a-198">Если используется центр развертывания Службы приложений для создания сборки Azure DevOps, конвейер сборки по умолчанию включает шаги для `Restore`, `Build`, `Test` и `Publish`.</span><span class="sxs-lookup"><span data-stu-id="f048a-198">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="f048a-199">Чтобы указать версию пакета SDK, нажмите кнопку **Добавить (+)** в списке заданий агента для добавления нового шага.</span><span class="sxs-lookup"><span data-stu-id="f048a-199">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="f048a-200">Найдите **пакет SDK для .NET Core**, используя строку поиска.</span><span class="sxs-lookup"><span data-stu-id="f048a-200">Search for **.NET Core SDK** in the search bar.</span></span> 

![Добавление шага для использования пакета SDK .NET Core](index/add-sdk-step.png)

<span data-ttu-id="f048a-202">Переместите шаг на первое место в конвейере сборки, чтобы в следующих шагах использовалась указанная версия пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-202">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="f048a-203">Укажите версию пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-203">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="f048a-204">В этом примере для пакета SDK мы укажем версию `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="f048a-204">In this example, the SDK is set to `3.0.100`.</span></span>

![Завершенный этап настройки пакета SDK](index/sdk-step-first-place.png)

<span data-ttu-id="f048a-206">Чтобы опубликовать [автономное развертывание (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), настройте SCD на шаге `Publish` и укажите [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f048a-206">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Автономная публикация](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="f048a-208">Развертывание автономного приложения для предварительной версии</span><span class="sxs-lookup"><span data-stu-id="f048a-208">Deploy a self-contained preview app</span></span>

<span data-ttu-id="f048a-209">Объект [автономного развертывания (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), который предназначен для предварительной версии среды выполнения, включает в развертывание среду выполнения предварительной версии.</span><span class="sxs-lookup"><span data-stu-id="f048a-209">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="f048a-210">При развертывании автономного приложения:</span><span class="sxs-lookup"><span data-stu-id="f048a-210">When deploying a self-contained app:</span></span>

* <span data-ttu-id="f048a-211">Сайт в Службе приложений Azure не требует [предварительной версии расширения сайта](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="f048a-211">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="f048a-212">Для публикации приложений следует использовать другой подход, нежели при публикации [зависящего от платформы развертывания (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="f048a-212">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="f048a-213">Следуйте указаниям в разделе [Развертывание автономного приложения](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="f048a-213">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="f048a-214">Использование Docker с веб-приложениями для контейнеров</span><span class="sxs-lookup"><span data-stu-id="f048a-214">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="f048a-215">[Центр Docker](https://hub.docker.com/r/microsoft/aspnetcore/) содержит образы Docker из последней предварительной версии.</span><span class="sxs-lookup"><span data-stu-id="f048a-215">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="f048a-216">Их можно использовать в качестве базового образа.</span><span class="sxs-lookup"><span data-stu-id="f048a-216">The images can be used as a base image.</span></span> <span data-ttu-id="f048a-217">Использование образа и развертывание в веб-приложениях для контейнеров выполняется как обычно.</span><span class="sxs-lookup"><span data-stu-id="f048a-217">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="f048a-218">Установка расширения сайта предварительной версии</span><span class="sxs-lookup"><span data-stu-id="f048a-218">Install the preview site extension</span></span>

<span data-ttu-id="f048a-219">Если у вас возникли проблемы при использовании расширения сайта предварительной версии, создайте запрос [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="f048a-219">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="f048a-220">Перейдите в Службу приложений на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-220">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="f048a-221">Выберите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="f048a-221">Select the web app.</span></span>
1. <span data-ttu-id="f048a-222">В поле поиска введите "ex" для фильтрации расширений ("Extensions") или прокрутите вниз список инструментов управления.</span><span class="sxs-lookup"><span data-stu-id="f048a-222">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="f048a-223">Выберите **Расширения**.</span><span class="sxs-lookup"><span data-stu-id="f048a-223">Select **Extensions**.</span></span>
1. <span data-ttu-id="f048a-224">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="f048a-224">Select **Add**.</span></span>
1. <span data-ttu-id="f048a-225">Выберите в списке расширение **Среда выполнения ASP.NET Core {X.Y} ({x64|x86})** , где `{X.Y}` — это предварительная версия ASP.NET Core, а `{x64|x86}` — платформа.</span><span class="sxs-lookup"><span data-stu-id="f048a-225">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="f048a-226">Для принятия условий нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="f048a-226">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="f048a-227">Нажмите **OK**, чтобы установить расширение.</span><span class="sxs-lookup"><span data-stu-id="f048a-227">Select **OK** to install the extension.</span></span>

<span data-ttu-id="f048a-228">По завершении операции устанавливается последняя предварительная версия .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-228">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="f048a-229">Проверьте установку:</span><span class="sxs-lookup"><span data-stu-id="f048a-229">Verify the installation:</span></span>

1. <span data-ttu-id="f048a-230">Выберите **Дополнительные инструменты**.</span><span class="sxs-lookup"><span data-stu-id="f048a-230">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="f048a-231">В разделе **Дополнительные инструменты** выберите **Перейти**.</span><span class="sxs-lookup"><span data-stu-id="f048a-231">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="f048a-232">Выберите пункт меню **Консоль отладки** > **PowerShell**.</span><span class="sxs-lookup"><span data-stu-id="f048a-232">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="f048a-233">По запросу PowerShell выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="f048a-233">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="f048a-234">В следующей команде вместо `{X.Y}` укажите версию среды выполнения ASP.NET Core, а вместо `{PLATFORM}` — платформу:</span><span class="sxs-lookup"><span data-stu-id="f048a-234">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="f048a-235">Эта команда возвращает `True`, если установлена предварительная версия среды выполнения x64.</span><span class="sxs-lookup"><span data-stu-id="f048a-235">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="f048a-236">Если приложение Службы приложений размещено в среде вычислений серии A (цен. категории "Базовый") или на более высоком уровне, архитектура платформы приложения (x86 или x64) задается в его параметрах на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-236">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="f048a-237">Убедитесь, что параметры публикации приложения, например в [профиле публикации Visual Studio (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles), соответствуют параметрам в конфигурации службы приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-237">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="f048a-238">Если приложение выполняется во внутрипроцессном режиме, а архитектура платформы настроена для 64-разрядных версий (x64), модуль ASP.NET Core использует 64-разрядную предварительную версию среды выполнения при ее наличии.</span><span class="sxs-lookup"><span data-stu-id="f048a-238">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="f048a-239">Установите расширение **Среда выполнения ASP.NET Core {X.Y} (x64)** с помощью портала Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-239">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="f048a-240">После установки предварительной версии среды выполнения (x64) выполните приведенную ниже команду в командном окне Azure Kudu PowerShell, чтобы проверить установку.</span><span class="sxs-lookup"><span data-stu-id="f048a-240">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="f048a-241">Замените версию среды выполнения ASP.NET Core на `{X.Y}` в следующей команде:</span><span class="sxs-lookup"><span data-stu-id="f048a-241">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="f048a-242">Эта команда возвращает `True`, если установлена предварительная версия среды выполнения x64.</span><span class="sxs-lookup"><span data-stu-id="f048a-242">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="f048a-243">**Расширения ASP.NET Core** включают дополнительные функциональные возможности для ASP.NET Core в службах приложений Azure, например включение ведения журналов Azure.</span><span class="sxs-lookup"><span data-stu-id="f048a-243">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="f048a-244">Расширение устанавливается автоматически при развертывании из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f048a-244">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="f048a-245">Если расширение не установлено, установите его для приложения.</span><span class="sxs-lookup"><span data-stu-id="f048a-245">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="f048a-246">**Использование расширения сайта предварительной версии с шаблоном ARM**</span><span class="sxs-lookup"><span data-stu-id="f048a-246">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="f048a-247">Если вы используете шаблон ARM для создания и развертывания приложений, можно использовать тип ресурса `siteextensions`, чтобы добавить расширение сайта в веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="f048a-247">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="f048a-248">Пример:</span><span class="sxs-lookup"><span data-stu-id="f048a-248">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="f048a-249">Публикация и развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="f048a-249">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f048a-250">Для развертывания 64-разрядной версии</span><span class="sxs-lookup"><span data-stu-id="f048a-250">For a 64-bit deployment:</span></span>

* <span data-ttu-id="f048a-251">Для создания 64-разрядной версии приложения используйте 64-разрядную версию пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f048a-251">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="f048a-252">В разделе **Конфигурация** > **Общие параметры** службы приложений выберите для параметра **Платформа** значение **64-разрядная версия**.</span><span class="sxs-lookup"><span data-stu-id="f048a-252">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="f048a-253">Чтобы включить возможность выбора разрядности платформы, приложение должно использовать план обслуживания "Базовый" или более высокий.</span><span class="sxs-lookup"><span data-stu-id="f048a-253">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="f048a-254">Развертывание приложения, зависимого от платформы</span><span class="sxs-lookup"><span data-stu-id="f048a-254">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f048a-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f048a-255">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f048a-256">Выберите **Сборка** > **Опубликовать {имя_приложения}** на панели инструментов Visual Studio или щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="f048a-256">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="f048a-257">В диалоговом оке **Выберите целевой объект публикации** убедитесь, что выбран вариант **Служба приложений**.</span><span class="sxs-lookup"><span data-stu-id="f048a-257">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="f048a-258">Выберите **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="f048a-258">Select **Advanced**.</span></span> <span data-ttu-id="f048a-259">Откроется диалоговое окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="f048a-259">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="f048a-260">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="f048a-260">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="f048a-261">Убедитесь, что выбрана конфигурация **Выпуск**.</span><span class="sxs-lookup"><span data-stu-id="f048a-261">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="f048a-262">Откройте раскрывающийся список **Режим развертывания** и выберите вариант **Зависит от платформы**.</span><span class="sxs-lookup"><span data-stu-id="f048a-262">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="f048a-263">Для параметра **Целевая среда выполнения** выберите значение **Переносимая**.</span><span class="sxs-lookup"><span data-stu-id="f048a-263">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="f048a-264">Если потребуется удалить дополнительные файлы после развертывания, откройте **Параметры публикации файлов** и установите флажок для удаления дополнительных файлов в месте назначения.</span><span class="sxs-lookup"><span data-stu-id="f048a-264">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="f048a-265">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="f048a-265">Select **Save**.</span></span>
1. <span data-ttu-id="f048a-266">Создайте новый сайт или обновите существующий, следуя остальным подсказкам мастера публикации.</span><span class="sxs-lookup"><span data-stu-id="f048a-266">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f048a-267">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f048a-267">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f048a-268">В файле проекта не указывайте [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f048a-268">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="f048a-269">Из командной оболочки опубликуйте приложение в конфигурации выпуска, выполнив команду [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="f048a-269">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f048a-270">В следующем примере приложение публикуется как зависимое от платформы:</span><span class="sxs-lookup"><span data-stu-id="f048a-270">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="f048a-271">Переместите содержимое каталога *bin/Release/{целевая_платформа}/publish* на сайт в Службе приложений.</span><span class="sxs-lookup"><span data-stu-id="f048a-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="f048a-272">Если вы перетаскиваете содержимое папки *publish* с локального жесткого диска или общей папки непосредственно в Службу приложений на консоли [Kudu](https://github.com/projectkudu/kudu/wiki), перетащите файлы в папку `D:\home\site\wwwroot` на консоли Kudu.</span><span class="sxs-lookup"><span data-stu-id="f048a-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="f048a-273">Автономное развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="f048a-273">Deploy the app self-contained</span></span>

<span data-ttu-id="f048a-274">Используйте Visual Studio или .NET Core CLI для [автономного развертывания (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f048a-274">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f048a-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f048a-275">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f048a-276">Выберите **Сборка** > **Опубликовать {имя_приложения}** на панели инструментов Visual Studio или щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="f048a-276">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="f048a-277">В диалоговом оке **Выберите целевой объект публикации** убедитесь, что выбран вариант **Служба приложений**.</span><span class="sxs-lookup"><span data-stu-id="f048a-277">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="f048a-278">Выберите **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="f048a-278">Select **Advanced**.</span></span> <span data-ttu-id="f048a-279">Откроется диалоговое окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="f048a-279">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="f048a-280">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="f048a-280">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="f048a-281">Убедитесь, что выбрана конфигурация **Выпуск**.</span><span class="sxs-lookup"><span data-stu-id="f048a-281">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="f048a-282">Откройте раскрывающийся список **Режим развертывания** и выберите вариант **Автономное**.</span><span class="sxs-lookup"><span data-stu-id="f048a-282">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="f048a-283">Выберите целевую среду выполнения из раскрывающегося списка **Целевая среда выполнения**.</span><span class="sxs-lookup"><span data-stu-id="f048a-283">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="f048a-284">Значение по умолчанию — `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="f048a-284">The default is `win-x86`.</span></span>
   * <span data-ttu-id="f048a-285">Если потребуется удалить дополнительные файлы после развертывания, откройте **Параметры публикации файлов** и установите флажок для удаления дополнительных файлов в месте назначения.</span><span class="sxs-lookup"><span data-stu-id="f048a-285">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="f048a-286">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="f048a-286">Select **Save**.</span></span>
1. <span data-ttu-id="f048a-287">Создайте новый сайт или обновите существующий, следуя остальным подсказкам мастера публикации.</span><span class="sxs-lookup"><span data-stu-id="f048a-287">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f048a-288">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="f048a-288">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f048a-289">В файле проекта укажите один или несколько [идентификаторов сред выполнения (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f048a-289">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="f048a-290">Используйте `<RuntimeIdentifier>` (в единственном числе) для одного RID или `<RuntimeIdentifiers>` (во множественном числе), чтобы предоставить разделенный точкой с запятой список идентификаторов RID.</span><span class="sxs-lookup"><span data-stu-id="f048a-290">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="f048a-291">В следующем примере указан RID `win-x86`:</span><span class="sxs-lookup"><span data-stu-id="f048a-291">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f048a-292">Из командной оболочки опубликуйте приложение в конфигурации выпуска для среды выполнения узла, выполнив команду [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="f048a-292">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f048a-293">В следующем примере публикуется приложение с RID `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="f048a-293">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="f048a-294">Предоставленный в параметре `--runtime` RID должены быть указан в свойстве `<RuntimeIdentifier>` (или `<RuntimeIdentifiers>`) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="f048a-294">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="f048a-295">Переместите содержимое каталога *bin/Release/{целевая_платформа}/{идентификатор_среды_выполнения}/publish* на сайт в Службе приложений.</span><span class="sxs-lookup"><span data-stu-id="f048a-295">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="f048a-296">Если вы перетаскиваете содержимое папки *publish* с локального жесткого диска или общей папки непосредственно в Службу приложений на консоли Kudu, перетащите файлы в папку `D:\home\site\wwwroot` на консоли Kudu.</span><span class="sxs-lookup"><span data-stu-id="f048a-296">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="f048a-297">Параметры протокола (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="f048a-297">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="f048a-298">Привязки безопасных протоколов позволяют указать сертификат, который следует использовать при ответе на запросы по HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f048a-298">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="f048a-299">Для привязки требуется допустимый закрытый сертификат (*PFX*), выданный для определенного имени узла.</span><span class="sxs-lookup"><span data-stu-id="f048a-299">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="f048a-300">Дополнительные сведения см. в статье [Руководство. Привязывание существующего настраиваемого SSL-сертификата к Службе приложений Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="f048a-300">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="f048a-301">Преобразование web.config</span><span class="sxs-lookup"><span data-stu-id="f048a-301">Transform web.config</span></span>

<span data-ttu-id="f048a-302">Если вам нужно преобразовать *web.config* при публикации (например, задать переменные среды на основе конфигурации, профиля или среды), см. раздел <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="f048a-302">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f048a-303">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f048a-303">Additional resources</span></span>

* [<span data-ttu-id="f048a-304">Обзор Службы приложений</span><span class="sxs-lookup"><span data-stu-id="f048a-304">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="f048a-305">Служба приложений Azure: оптимальное место для размещения приложений .NET (55-минутное видео)</span><span class="sxs-lookup"><span data-stu-id="f048a-305">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="f048a-306">Пятница с Azure. Практика диагностики и устранения неполадок в Службе приложений Azure (12-минутное видео)</span><span class="sxs-lookup"><span data-stu-id="f048a-306">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="f048a-307">Общие сведения о диагностике в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="f048a-307">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="f048a-308">Служба приложений Azure на Windows Server использует [службы IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f048a-308">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="f048a-309">Технологии IIS посвящены следующие статьи.</span><span class="sxs-lookup"><span data-stu-id="f048a-309">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="f048a-310">Windows Server — содержимое для ИТ-администраторов по текущим и предыдущим выпускам</span><span class="sxs-lookup"><span data-stu-id="f048a-310">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
