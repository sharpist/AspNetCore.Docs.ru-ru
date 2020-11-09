---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio Code
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio Code
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061266"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="5d759-103">Публикация приложения ASP.NET Core в Azure с помощью Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d759-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="5d759-104">Автор: [Рикардо Серрадас](https://twitter.com/ricardoserradas) (Ricardo Serradas)</span><span class="sxs-lookup"><span data-stu-id="5d759-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="5d759-105">Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5d759-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="5d759-106">Введение</span><span class="sxs-lookup"><span data-stu-id="5d759-106">Intro</span></span>

<span data-ttu-id="5d759-107">С помощью этого руководства вы узнаете, как создать приложение MVC ASP.Net Core и развернуть его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d759-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="5d759-108">Настройка</span><span class="sxs-lookup"><span data-stu-id="5d759-108">Set up</span></span>

- <span data-ttu-id="5d759-109">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="5d759-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="5d759-110">Установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5d759-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="5d759-111">Установка [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="5d759-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="5d759-112">Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) для Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d759-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="5d759-113">Установите [расширение службы приложений Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) для Visual Studio Code и настройте его перед продолжением</span><span class="sxs-lookup"><span data-stu-id="5d759-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="5d759-114">Создание проекта MVC ASP.Net Core</span><span class="sxs-lookup"><span data-stu-id="5d759-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="5d759-115">Используя терминал, перейдите в папку для создания проекта и используйте следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5d759-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="5d759-116">Вы получите структуру папок, аналогичную следующей:</span><span class="sxs-lookup"><span data-stu-id="5d759-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="5d759-117">Открытие проекта с помощью Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d759-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="5d759-118">После создания проекта его можно открыть с помощью Visual Studio Code, используя один из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="5d759-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="5d759-119">С помощью командной строки</span><span class="sxs-lookup"><span data-stu-id="5d759-119">Through the command line</span></span>

<span data-ttu-id="5d759-120">Используйте следующую команду в папке, где создан проект:</span><span class="sxs-lookup"><span data-stu-id="5d759-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="5d759-121">Если приведенная ниже команда не работает, убедитесь в правильной настройке установки (перейдите по [этой ссылке](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)).</span><span class="sxs-lookup"><span data-stu-id="5d759-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="5d759-122">С помощью интерфейса Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5d759-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="5d759-123">Откройте Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d759-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="5d759-124">В меню выберите `File > Open Folder`.</span><span class="sxs-lookup"><span data-stu-id="5d759-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="5d759-125">Выберите корень папки, в которой создан проект MVC.</span><span class="sxs-lookup"><span data-stu-id="5d759-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="5d759-126">При открытии папки проекта вы получите сообщение о том, что отсутствуют необходимые ресурсы для сборки и отладки.</span><span class="sxs-lookup"><span data-stu-id="5d759-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="5d759-127">Примите приглашение справки, чтобы добавить их.</span><span class="sxs-lookup"><span data-stu-id="5d759-127">Accept the help to add them.</span></span>

![Интерфейс Visual Studio Code с загруженным проектом](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="5d759-129">Папка `.vscode` будет создана в структуре проекта.</span><span class="sxs-lookup"><span data-stu-id="5d759-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="5d759-130">Она будет содержать следующие файлы:</span><span class="sxs-lookup"><span data-stu-id="5d759-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="5d759-131">Это служебные файлы, которые помогут вам создать веб-приложение .NET Core и выполнить его отладку.</span><span class="sxs-lookup"><span data-stu-id="5d759-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5d759-132">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5d759-132">Run the app</span></span>

<span data-ttu-id="5d759-133">Перед развертыванием приложения в Azure убедитесь, что оно работает правильно на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="5d759-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="5d759-134">Нажмите клавишу F5, чтобы запустить проект.</span><span class="sxs-lookup"><span data-stu-id="5d759-134">Press F5 to run the project</span></span>

<span data-ttu-id="5d759-135">Веб-приложение начнет выполняться на новой вкладке браузера по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d759-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="5d759-136">Сразу после его запуска может появиться предупреждение о конфиденциальности.</span><span class="sxs-lookup"><span data-stu-id="5d759-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="5d759-137">Это происходит потому, что ваше приложение запускается с помощью HTTP или HTTPS и оно переходит к конечной точке HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d759-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Предупреждение о конфиденциальности при локальной отладке приложения](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="5d759-139">Чтобы сохранить сеанс отладки, нажмите кнопку `Advanced`, а затем `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="5d759-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="5d759-140">Создание пакета развертывания локально</span><span class="sxs-lookup"><span data-stu-id="5d759-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="5d759-141">Откройте терминал Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5d759-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="5d759-142">Используйте следующую команду, чтобы создать пакет `Release` во вложенной папке с именем `publish`:</span><span class="sxs-lookup"><span data-stu-id="5d759-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="5d759-143">Новая папка `publish` будет создана в структуре проекта.</span><span class="sxs-lookup"><span data-stu-id="5d759-143">A new `publish` folder will be created under the project structure</span></span>

![Структура папки публикации](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="5d759-145">Публикация в службу приложений Azure</span><span class="sxs-lookup"><span data-stu-id="5d759-145">Publish to Azure App Service</span></span>

<span data-ttu-id="5d759-146">Используя расширение службы приложений Azure для Visual Studio Code, выполните следующие действия, чтобы опубликовать веб-сайт непосредственно в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="5d759-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="5d759-147">При создании нового веб-приложения</span><span class="sxs-lookup"><span data-stu-id="5d759-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="5d759-148">Щелкните папку `publish` правой кнопкой мыши и выберите `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="5d759-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5d759-149">Выберите подписку, в которой нужно создать веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="5d759-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="5d759-150">Выберите `Create New Web App`.</span><span class="sxs-lookup"><span data-stu-id="5d759-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="5d759-151">Введите имя веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5d759-151">Enter a name for the Web App</span></span>

<span data-ttu-id="5d759-152">Расширение создаст веб-приложение и автоматически начнет развертывание пакета для приложения.</span><span class="sxs-lookup"><span data-stu-id="5d759-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="5d759-153">После завершения развертывания щелкните `Browse Website`, чтобы проверить развертывание.</span><span class="sxs-lookup"><span data-stu-id="5d759-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Сообщение об успешно выполненном развертывании](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="5d759-155">После нажатия кнопки `Browse Website` вы перейдете к веб-сайту с помощью браузера по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="5d759-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Новое веб-приложение успешно развернуто](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="5d759-157">При развертывании в существующее веб-приложение</span><span class="sxs-lookup"><span data-stu-id="5d759-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="5d759-158">Щелкните папку `publish` правой кнопкой мыши и выберите `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="5d759-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5d759-159">Выберите подписку, в которой находится существующее веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="5d759-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="5d759-160">Выберите веб-приложение в списке.</span><span class="sxs-lookup"><span data-stu-id="5d759-160">Select the Web App from the list</span></span>
- <span data-ttu-id="5d759-161">Visual Studio Code выдаст запрос на перезапись существующего содержимого.</span><span class="sxs-lookup"><span data-stu-id="5d759-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="5d759-162">Щелкните `Deploy` для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="5d759-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="5d759-163">Расширение выполнит развертывание обновленного содержимого веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="5d759-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="5d759-164">После этого щелкните `Browse Website`, чтобы проверить развертывание.</span><span class="sxs-lookup"><span data-stu-id="5d759-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Существующее веб-приложение успешно развернуто](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="5d759-166">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="5d759-166">Next steps</span></span>

- [<span data-ttu-id="5d759-167">Создание первого конвейера DevOps в Azure</span><span class="sxs-lookup"><span data-stu-id="5d759-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="5d759-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5d759-168">Additional resources</span></span>

- <span data-ttu-id="5d759-169">[служба приложений Azure](/azure/app-service/app-service-web-overview);</span><span class="sxs-lookup"><span data-stu-id="5d759-169">[Azure App Service](/azure/app-service/app-service-web-overview)</span></span>
- [<span data-ttu-id="5d759-170">Группа ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="5d759-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
