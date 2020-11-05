---
title: Публикация приложения ASP.NET Core в службах IIS
author: rick-anderson
description: Узнайте, как разместить приложение ASP.NET Core на сервере служб IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060512"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="49b3b-103">Публикация приложения ASP.NET Core в службах IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="49b3b-104">В этом руководстве описывается, как разместить приложение ASP.NET Core на сервере служб IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="49b3b-105">В руководстве рассматриваются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="49b3b-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49b3b-106">установка пакета размещения .NET Core в Windows Server;</span><span class="sxs-lookup"><span data-stu-id="49b3b-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="49b3b-107">создание сайта служб IIS в диспетчере служб IIS;</span><span class="sxs-lookup"><span data-stu-id="49b3b-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="49b3b-108">развертывание приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b3b-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49b3b-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="49b3b-109">Prerequisites</span></span>

* <span data-ttu-id="49b3b-110">[Пакет SDK для .NET Core](/dotnet/core/sdk), установленный на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="49b3b-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="49b3b-111">Сервер Windows Server с настроенной ролью **Веб-сервер (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="49b3b-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="49b3b-112">Если сервер не настроен для размещения веб-сайтов со службами IIS, следуйте указаниям в разделе *Настройка служб IIS* статьи <xref:host-and-deploy/iis/index#iis-configuration>, а затем вернитесь к этому руководству.</span><span class="sxs-lookup"><span data-stu-id="49b3b-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="49b3b-113">**Принципы настройки служб IIS и обеспечения безопасности веб-сайта не рассматриваются в этом руководстве.**</span><span class="sxs-lookup"><span data-stu-id="49b3b-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="49b3b-114">Перед размещением рабочих приложений в службах IIS ознакомьтесь с руководством по службам IIS в [документации по Microsoft IIS](https://www.iis.net/) и [статьей о размещении ASP.NET Core с помощью служб IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="49b3b-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="49b3b-115">Важные сценарии размещения служб IIS, не рассматриваемые в этом руководстве:</span><span class="sxs-lookup"><span data-stu-id="49b3b-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="49b3b-116">Создание куста реестра для защиты данных ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="49b3b-117">Настройка списка управления доступом (ACL) для пула приложений</span><span class="sxs-lookup"><span data-stu-id="49b3b-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="49b3b-118">Чтобы сосредоточиться на принципах развертывания посредством служб IIS, в этом руководстве приложение развертывается без настройки протокола HTTPS в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="49b3b-119">Дополнительные сведения о размещении приложения с поддержкой протокола HTTPS см. в статьях, посвященных безопасности, в разделе [Дополнительные ресурсы](#additional-resources) этой статьи.</span><span class="sxs-lookup"><span data-stu-id="49b3b-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="49b3b-120">Дополнительные рекомендации по размещению приложений ASP.NET Core приведены в статье <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="49b3b-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="49b3b-121">Установка пакета размещения .NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="49b3b-122">Установите *пакет размещения .NET Core* на сервере служб IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="49b3b-123">В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="49b3b-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="49b3b-124">Модуль позволяет запускать приложения ASP.NET Core под управлением IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="49b3b-125">Скачайте установщик по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="49b3b-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="49b3b-126">Текущий установщик пакета размещения .NET Core (прямая загрузка)</span><span class="sxs-lookup"><span data-stu-id="49b3b-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="49b3b-127">Запустите установщик на сервере служб IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="49b3b-128">Перезагрузите сервер или в командой оболочке выполните команду `net stop was /y`, а затем — `net start w3svc`.</span><span class="sxs-lookup"><span data-stu-id="49b3b-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="49b3b-129">Создание сайта IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-129">Create the IIS site</span></span>

1. <span data-ttu-id="49b3b-130">На сервере служб IIS создайте папку, в которой будут храниться файлы и папки опубликованного приложения.</span><span class="sxs-lookup"><span data-stu-id="49b3b-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="49b3b-131">На следующем этапе путь к папке предоставляется IIS как физический путь к приложению.</span><span class="sxs-lookup"><span data-stu-id="49b3b-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="49b3b-132">Дополнительные сведения о папке развертывания и структуре файлов приложения см. в статье <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="49b3b-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="49b3b-133">В окне диспетчера IIS на панели **Подключения** разверните узел сервера.</span><span class="sxs-lookup"><span data-stu-id="49b3b-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="49b3b-134">Щелкните правой кнопкой мыши папку **Сайты**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="49b3b-135">В контекстном меню выберите пункт **Добавить веб-сайт**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="49b3b-136">Укажите **Имя сайта** и задайте **Физический путь** к созданной папке развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="49b3b-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="49b3b-137">Укажите конфигурацию **привязки** и нажмите кнопку **ОК** , чтобы создать веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="49b3b-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

   > [!WARNING]
   > <span data-ttu-id="49b3b-138">**Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне.</span><span class="sxs-lookup"><span data-stu-id="49b3b-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="49b3b-139">Это может создать уязвимость и поставить ваше приложение под угрозу.</span><span class="sxs-lookup"><span data-stu-id="49b3b-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="49b3b-140">Сюда относятся и строгие, и нестрогие подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="49b3b-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="49b3b-141">Вместо этого используйте имена узлов в явном виде.</span><span class="sxs-lookup"><span data-stu-id="49b3b-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="49b3b-142">Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="49b3b-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="49b3b-143">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="49b3b-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="49b3b-144">Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.</span><span class="sxs-lookup"><span data-stu-id="49b3b-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="49b3b-145">При изменении удостоверения по умолчанию для пула приложений ( **Модель процесса** >  **:::no-loc(Identity):::** ) с `ApplicationPool:::no-loc(Identity):::` на другое убедитесь в том, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам.</span><span class="sxs-lookup"><span data-stu-id="49b3b-145">If the default identity of the app pool ( **Process Model** > **:::no-loc(Identity):::** ) is changed from `ApplicationPool:::no-loc(Identity):::` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="49b3b-146">Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.</span><span class="sxs-lookup"><span data-stu-id="49b3b-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="49b3b-147">Создание приложения :::no-loc(Razor)::: Pages ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-147">Create an ASP.NET Core :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="49b3b-148">Следуйте указаниям из руководства <xref:getting-started>, чтобы создать приложение :::no-loc(Razor)::: Pages.</span><span class="sxs-lookup"><span data-stu-id="49b3b-148">Follow the <xref:getting-started> tutorial to create a :::no-loc(Razor)::: Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="49b3b-149">Публикация и развертывание приложения</span><span class="sxs-lookup"><span data-stu-id="49b3b-149">Publish and deploy the app</span></span>

<span data-ttu-id="49b3b-150">*Публикация приложения* означает создание скомпилированного приложения, которое можно разместить на сервере.</span><span class="sxs-lookup"><span data-stu-id="49b3b-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="49b3b-151">*Развертывание приложения* означает перемещение опубликованного приложения в систему размещения.</span><span class="sxs-lookup"><span data-stu-id="49b3b-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="49b3b-152">Публикация обеспечивается [пакетом SDK для .NET Core](/dotnet/core/sdk), а развертывание может производиться различными способами.</span><span class="sxs-lookup"><span data-stu-id="49b3b-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="49b3b-153">В этом руководстве применяется развертывание с помощью *папок* , которое состоит из следующих этапов:</span><span class="sxs-lookup"><span data-stu-id="49b3b-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="49b3b-154">Приложение публикуется в папке.</span><span class="sxs-lookup"><span data-stu-id="49b3b-154">The app is published to a folder.</span></span>
* <span data-ttu-id="49b3b-155">Содержимое папки перемещается в папку на сайте IIS ( **физический путь** к сайту в диспетчере IIS).</span><span class="sxs-lookup"><span data-stu-id="49b3b-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="49b3b-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="49b3b-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="49b3b-157">В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-157">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="49b3b-158">В диалоговом оке **Выберите целевой объект публикации** выберите вариант публикации **Папка**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="49b3b-159">Укажите путь к **папке или общей папке**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="49b3b-160">Если вы создали для сайта IIS папку, доступную на компьютере разработки в качестве сетевой папки, укажите путь к общей папке.</span><span class="sxs-lookup"><span data-stu-id="49b3b-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="49b3b-161">Для публикации в общей папке текущий пользователь должен иметь доступ на запись.</span><span class="sxs-lookup"><span data-stu-id="49b3b-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="49b3b-162">Если выполнить развертывание непосредственно в папке сайта IIS на сервере IIS невозможно, опубликуйте приложение в папке на съемном носителе и физически переместите опубликованное приложение в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="49b3b-163">Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="49b3b-164">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="49b3b-165">В командной оболочке опубликуйте приложение в конфигурации выпуска, выполнив команду [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="49b3b-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="49b3b-166">Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="49b3b-167">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="49b3b-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="49b3b-168">В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите пункты **Опубликовать** > **Опубликовать в папку**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="49b3b-169">Укажите путь в поле **Выберите папку**.</span><span class="sxs-lookup"><span data-stu-id="49b3b-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="49b3b-170">Если вы создали для сайта IIS папку, доступную на компьютере разработки в качестве сетевой папки, укажите путь к общей папке.</span><span class="sxs-lookup"><span data-stu-id="49b3b-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="49b3b-171">Для публикации в общей папке текущий пользователь должен иметь доступ на запись.</span><span class="sxs-lookup"><span data-stu-id="49b3b-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="49b3b-172">Если выполнить развертывание непосредственно в папке сайта IIS на сервере IIS невозможно, опубликуйте приложение в папке на съемном носителе и физически переместите опубликованное приложение в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="49b3b-173">Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.</span><span class="sxs-lookup"><span data-stu-id="49b3b-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="49b3b-174">Обзор веб-сайта</span><span class="sxs-lookup"><span data-stu-id="49b3b-174">Browse the website</span></span>

<span data-ttu-id="49b3b-175">Приложение будет доступно в браузере после получения первого запроса.</span><span class="sxs-lookup"><span data-stu-id="49b3b-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="49b3b-176">Выполните запрос к приложению в привязке конечной точки, созданной в диспетчере служб IIS для сайта.</span><span class="sxs-lookup"><span data-stu-id="49b3b-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="49b3b-177">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="49b3b-177">Next steps</span></span>

<span data-ttu-id="49b3b-178">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="49b3b-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="49b3b-179">установка пакета размещения .NET Core в Windows Server;</span><span class="sxs-lookup"><span data-stu-id="49b3b-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="49b3b-180">создание сайта служб IIS в диспетчере служб IIS;</span><span class="sxs-lookup"><span data-stu-id="49b3b-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="49b3b-181">развертывание приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49b3b-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="49b3b-182">Дополнительные сведения о размещении приложений ASP.NET Core в службах IIS см. в обзорной статье о службах IIS:</span><span class="sxs-lookup"><span data-stu-id="49b3b-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="49b3b-183">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="49b3b-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="49b3b-184">Статьи в наборе документации по ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="49b3b-185">Статьи, относящиеся к развертыванию приложений ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="49b3b-186">Публикация веб-приложения в папку с помощью Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="49b3b-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="49b3b-187">Статьи о конфигурации HTTPS служб IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="49b3b-188">Настройка SSL в диспетчере служб IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="49b3b-189">Настройка SSL в службах IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="49b3b-190">Статьи о службах IIS и Windows Server</span><span class="sxs-lookup"><span data-stu-id="49b3b-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="49b3b-191">Официальный веб-сайт Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="49b3b-192">Библиотека технического содержимого по Windows Server</span><span class="sxs-lookup"><span data-stu-id="49b3b-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="49b3b-193">Ресурсы развертывания для администраторов IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="49b3b-194">Документация по службам IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-194">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="49b3b-195">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="49b3b-195">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="49b3b-196">Развертывание приложений .NET Core</span><span class="sxs-lookup"><span data-stu-id="49b3b-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

