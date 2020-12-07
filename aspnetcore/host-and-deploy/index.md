---
title: Размещение и развертывание ASP.NET Core
author: rick-anderson
description: Сведения о настройке сред размещения и развертывании приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556623"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="fdaba-103">Размещение и развертывание ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fdaba-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fdaba-104">В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="fdaba-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="fdaba-105">Развертывание опубликованного приложения в папке на сервере размещения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="fdaba-106">Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="fdaba-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="fdaba-107">Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.</span><span class="sxs-lookup"><span data-stu-id="fdaba-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="fdaba-108">Публикация в папку</span><span class="sxs-lookup"><span data-stu-id="fdaba-108">Publish to a folder</span></span>

<span data-ttu-id="fdaba-109">Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*.</span><span class="sxs-lookup"><span data-stu-id="fdaba-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="fdaba-110">При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.</span><span class="sxs-lookup"><span data-stu-id="fdaba-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="fdaba-111">Файлы параметров публикации</span><span class="sxs-lookup"><span data-stu-id="fdaba-111">Publish settings files</span></span>

<span data-ttu-id="fdaba-112">Файлы `*.json` публикуются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fdaba-112">`*.json` files are published by default.</span></span> <span data-ttu-id="fdaba-113">Чтобы опубликовать другие файлы параметров, укажите их в элементе [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="fdaba-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="fdaba-114">В следующем примере публикуются XML-файлы:</span><span class="sxs-lookup"><span data-stu-id="fdaba-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="fdaba-115">Содержимое папки</span><span class="sxs-lookup"><span data-stu-id="fdaba-115">Folder contents</span></span>

<span data-ttu-id="fdaba-116">Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="fdaba-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="fdaba-117">Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*.</span><span class="sxs-lookup"><span data-stu-id="fdaba-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="fdaba-118">Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="fdaba-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="fdaba-119">Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере.</span><span class="sxs-lookup"><span data-stu-id="fdaba-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="fdaba-120">По умолчанию используется модель развертывания с зависимостью от платформы.</span><span class="sxs-lookup"><span data-stu-id="fdaba-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="fdaba-121">Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fdaba-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="fdaba-122">В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC.</span><span class="sxs-lookup"><span data-stu-id="fdaba-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="fdaba-123">Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="fdaba-124">Настройка диспетчер процессов</span><span class="sxs-lookup"><span data-stu-id="fdaba-124">Set up a process manager</span></span>

<span data-ttu-id="fdaba-125">Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="fdaba-126">Для автоматического запуска и перезапуска требуется диспетчер процессов.</span><span class="sxs-lookup"><span data-stu-id="fdaba-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="fdaba-127">Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fdaba-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="fdaba-128">Linux</span><span class="sxs-lookup"><span data-stu-id="fdaba-128">Linux</span></span>
  * [<span data-ttu-id="fdaba-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="fdaba-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="fdaba-130">Apache</span><span class="sxs-lookup"><span data-stu-id="fdaba-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="fdaba-131">Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-131">Windows</span></span>
  * [<span data-ttu-id="fdaba-132">Службы IIS</span><span class="sxs-lookup"><span data-stu-id="fdaba-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="fdaba-133">Служба Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="fdaba-134">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="fdaba-134">Set up a reverse proxy</span></span>

<span data-ttu-id="fdaba-135">Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="fdaba-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="fdaba-136">Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdaba-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="fdaba-137">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="fdaba-138">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="fdaba-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fdaba-139">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="fdaba-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fdaba-140">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="fdaba-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="fdaba-141">Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="fdaba-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="fdaba-142">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="fdaba-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="fdaba-143">Использование Visual Studio и MSBuild для автоматизации развертывания</span><span class="sxs-lookup"><span data-stu-id="fdaba-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="fdaba-144">Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач.</span><span class="sxs-lookup"><span data-stu-id="fdaba-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="fdaba-145">Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее.</span><span class="sxs-lookup"><span data-stu-id="fdaba-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="fdaba-146">Visual Studio использует для веб-развертывания [MSBuild](/visualstudio/msbuild/msbuild) и настраивает MSBuild для решения многих других задач в процессе развертывания.</span><span class="sxs-lookup"><span data-stu-id="fdaba-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="fdaba-147">Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).</span><span class="sxs-lookup"><span data-stu-id="fdaba-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="fdaba-148">Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="fdaba-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="fdaba-149">Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="fdaba-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="fdaba-150">Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="fdaba-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="fdaba-151">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="fdaba-151">Publish to Azure</span></span>

<span data-ttu-id="fdaba-152">См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>).</span><span class="sxs-lookup"><span data-stu-id="fdaba-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="fdaba-153">Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="fdaba-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="fdaba-154">Публикация с помощью MSDeploy в Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="fdaba-155">Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="fdaba-156">службы IIS</span><span class="sxs-lookup"><span data-stu-id="fdaba-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="fdaba-157">Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="fdaba-158">Размещение в веб-ферме</span><span class="sxs-lookup"><span data-stu-id="fdaba-158">Host in a web farm</span></span>

<span data-ttu-id="fdaba-159">Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="fdaba-160">Размещение в Docker</span><span class="sxs-lookup"><span data-stu-id="fdaba-160">Host on Docker</span></span>

<span data-ttu-id="fdaba-161">Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="fdaba-162">Выполнение проверок работоспособности</span><span class="sxs-lookup"><span data-stu-id="fdaba-162">Perform health checks</span></span>

<span data-ttu-id="fdaba-163">Используйте ПО промежуточного слоя для проверки работоспособности приложения и его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fdaba-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="fdaba-164">Для получения дополнительной информации см. <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fdaba-165">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fdaba-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="fdaba-166">Размещение в ASP.NET</span><span class="sxs-lookup"><span data-stu-id="fdaba-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fdaba-167">В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="fdaba-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="fdaba-168">Развертывание опубликованного приложения в папке на сервере размещения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="fdaba-169">Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="fdaba-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="fdaba-170">Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.</span><span class="sxs-lookup"><span data-stu-id="fdaba-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="fdaba-171">Публикация в папку</span><span class="sxs-lookup"><span data-stu-id="fdaba-171">Publish to a folder</span></span>

<span data-ttu-id="fdaba-172">Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*.</span><span class="sxs-lookup"><span data-stu-id="fdaba-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="fdaba-173">При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.</span><span class="sxs-lookup"><span data-stu-id="fdaba-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="fdaba-174">Содержимое папки</span><span class="sxs-lookup"><span data-stu-id="fdaba-174">Folder contents</span></span>

<span data-ttu-id="fdaba-175">Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="fdaba-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="fdaba-176">Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*.</span><span class="sxs-lookup"><span data-stu-id="fdaba-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="fdaba-177">Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="fdaba-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="fdaba-178">Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере.</span><span class="sxs-lookup"><span data-stu-id="fdaba-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="fdaba-179">По умолчанию используется модель развертывания с зависимостью от платформы.</span><span class="sxs-lookup"><span data-stu-id="fdaba-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="fdaba-180">Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="fdaba-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="fdaba-181">В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC.</span><span class="sxs-lookup"><span data-stu-id="fdaba-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="fdaba-182">Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="fdaba-183">Настройка диспетчер процессов</span><span class="sxs-lookup"><span data-stu-id="fdaba-183">Set up a process manager</span></span>

<span data-ttu-id="fdaba-184">Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="fdaba-185">Для автоматического запуска и перезапуска требуется диспетчер процессов.</span><span class="sxs-lookup"><span data-stu-id="fdaba-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="fdaba-186">Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fdaba-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="fdaba-187">Linux</span><span class="sxs-lookup"><span data-stu-id="fdaba-187">Linux</span></span>
  * [<span data-ttu-id="fdaba-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="fdaba-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="fdaba-189">Apache</span><span class="sxs-lookup"><span data-stu-id="fdaba-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="fdaba-190">Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-190">Windows</span></span>
  * [<span data-ttu-id="fdaba-191">Службы IIS</span><span class="sxs-lookup"><span data-stu-id="fdaba-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="fdaba-192">Служба Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="fdaba-193">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="fdaba-193">Set up a reverse proxy</span></span>

<span data-ttu-id="fdaba-194">Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="fdaba-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="fdaba-195">Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fdaba-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="fdaba-196">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="fdaba-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="fdaba-197">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="fdaba-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fdaba-198">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="fdaba-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fdaba-199">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="fdaba-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="fdaba-200">Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="fdaba-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="fdaba-201">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="fdaba-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="fdaba-202">Использование Visual Studio и MSBuild для автоматизации развертывания</span><span class="sxs-lookup"><span data-stu-id="fdaba-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="fdaba-203">Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач.</span><span class="sxs-lookup"><span data-stu-id="fdaba-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="fdaba-204">Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее.</span><span class="sxs-lookup"><span data-stu-id="fdaba-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="fdaba-205">Visual Studio использует для веб-развертывания MSBuild и настраивает MSBuild для решения многих других задач в процессе развертывания.</span><span class="sxs-lookup"><span data-stu-id="fdaba-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="fdaba-206">Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).</span><span class="sxs-lookup"><span data-stu-id="fdaba-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="fdaba-207">Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="fdaba-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="fdaba-208">Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="fdaba-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="fdaba-209">Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="fdaba-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="fdaba-210">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="fdaba-210">Publish to Azure</span></span>

<span data-ttu-id="fdaba-211">См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>).</span><span class="sxs-lookup"><span data-stu-id="fdaba-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="fdaba-212">Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="fdaba-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="fdaba-213">Публикация с помощью MSDeploy в Windows</span><span class="sxs-lookup"><span data-stu-id="fdaba-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="fdaba-214">Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="fdaba-215">службы IIS</span><span class="sxs-lookup"><span data-stu-id="fdaba-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="fdaba-216">Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="fdaba-217">Размещение в веб-ферме</span><span class="sxs-lookup"><span data-stu-id="fdaba-217">Host in a web farm</span></span>

<span data-ttu-id="fdaba-218">Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="fdaba-219">Размещение в Docker</span><span class="sxs-lookup"><span data-stu-id="fdaba-219">Host on Docker</span></span>

<span data-ttu-id="fdaba-220">Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="fdaba-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fdaba-221">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fdaba-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="fdaba-222">Размещение в ASP.NET</span><span class="sxs-lookup"><span data-stu-id="fdaba-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
