---
title: Развертывание приложения в службу приложений — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Развертывание приложения ASP.NET Core в службу приложений Azure является первым шагов в использовании DevOps с ASP.NET Core и Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: 811b6d047e344fa98ce14f436d3cd8f03c786aff
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767035"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="eac4d-103">Развертывание приложения в службу приложений</span><span class="sxs-lookup"><span data-stu-id="eac4d-103">Deploy an app to App Service</span></span>

<span data-ttu-id="eac4d-104">[Служба приложений Azure](/azure/app-service/) — это платформа Azure для размещения веб-сайтов.</span><span class="sxs-lookup"><span data-stu-id="eac4d-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="eac4d-105">Развертывание веб-приложения в службу приложений Azure можно выполнять вручную или автоматически.</span><span class="sxs-lookup"><span data-stu-id="eac4d-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="eac4d-106">В этом разделе руководства обсуждаются методы развертывания, которые могут запускаться вручную или с помощью сценария из командной строки либо запускаться вручную с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eac4d-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="eac4d-107">В этом разделе мы выполним следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="eac4d-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="eac4d-108">скачаем и создадим пример приложения;</span><span class="sxs-lookup"><span data-stu-id="eac4d-108">Download and build the sample app.</span></span>
* <span data-ttu-id="eac4d-109">создадим веб-приложение службы приложений Azure с помощью Azure Cloud Shell;</span><span class="sxs-lookup"><span data-stu-id="eac4d-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="eac4d-110">развернем пример приложения в Azure;</span><span class="sxs-lookup"><span data-stu-id="eac4d-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="eac4d-111">развернем изменения в приложении с помощью Visual Studio;</span><span class="sxs-lookup"><span data-stu-id="eac4d-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="eac4d-112">добавим в веб-приложение промежуточный слот;</span><span class="sxs-lookup"><span data-stu-id="eac4d-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="eac4d-113">развернем обновление в промежуточном слоте;</span><span class="sxs-lookup"><span data-stu-id="eac4d-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="eac4d-114">переключим промежуточный и рабочий слоты.</span><span class="sxs-lookup"><span data-stu-id="eac4d-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="eac4d-115">Загрузка и тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="eac4d-115">Download and test the app</span></span>

<span data-ttu-id="eac4d-116">Приложение, используемое в этом руководстве, является предварительно созданным приложением ASP.NET Core — [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="eac4d-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="eac4d-117">Это приложение Razor Pages, которое использует API `Microsoft.SyndicationFeed.ReaderWriter` для получения канала RSS/Atom и отображает элементы новостей в списке.</span><span class="sxs-lookup"><span data-stu-id="eac4d-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="eac4d-118">Вы можете проанализировать код, но важно понимать, что в этом приложении нет ничего особенного.</span><span class="sxs-lookup"><span data-stu-id="eac4d-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="eac4d-119">Это простое приложение ASP.NET Core для иллюстративных целей.</span><span class="sxs-lookup"><span data-stu-id="eac4d-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="eac4d-120">В командной оболочке скачайте код, выполните сборку проекта и запустите его, как указано ниже.</span><span class="sxs-lookup"><span data-stu-id="eac4d-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="eac4d-121">*Примечание. Пользователи Linux и macOS должны внести соответствующие изменения в пути, например, используя косую черту (`/`) вместо обратной косой черты (`\`).*</span><span class="sxs-lookup"><span data-stu-id="eac4d-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="eac4d-122">Клонируйте код в папку на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="eac4d-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="eac4d-123">Измените рабочую папку на созданную папку *simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="eac4d-124">Восстановите пакеты и выполните сборку решения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="eac4d-125">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="eac4d-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Команда dotnet run успешно выполнена](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="eac4d-127">Откройте веб-браузер и перейдите по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="eac4d-128">Приложение позволяет вводить или вставлять URL-адрес канала синдикации и просматривать список новостей.</span><span class="sxs-lookup"><span data-stu-id="eac4d-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Приложение, отображающее содержимое RSS-канала](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="eac4d-130">Когда вы удостоверитесь, что приложение работает правильно, закройте его, нажав клавиши **CTRL**+**C** в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="eac4d-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="eac4d-131">Создание веб-приложения службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="eac4d-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="eac4d-132">Чтобы развернуть приложение, необходимо создать службу приложений [веб-приложение](/azure/app-service/app-service-web-overview).</span><span class="sxs-lookup"><span data-stu-id="eac4d-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="eac4d-133">После создания веб-приложения вы сможете развернуть его на локальном компьютере с помощью Git.</span><span class="sxs-lookup"><span data-stu-id="eac4d-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="eac4d-134">Войдите в [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="eac4d-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="eac4d-135">Примечание. При первом входе Cloud Shell предложит создать учетную запись хранения для файлов конфигурации.</span><span class="sxs-lookup"><span data-stu-id="eac4d-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="eac4d-136">Примите значения по умолчанию или укажите уникальное имя.</span><span class="sxs-lookup"><span data-stu-id="eac4d-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="eac4d-137">Для выполнения следующих действий используйте Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="eac4d-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="eac4d-138">1\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-138">a.</span></span> <span data-ttu-id="eac4d-139">Объявите переменную для хранения имени веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="eac4d-140">Имя должно быть уникальным для использования в URL-адресе по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="eac4d-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="eac4d-141">Использование функции Bash `$RANDOM` для создания имени гарантирует уникальность и результаты в формате `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="eac4d-142">2\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-142">b.</span></span> <span data-ttu-id="eac4d-143">Создайте группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="eac4d-143">Create a resource group.</span></span> <span data-ttu-id="eac4d-144">Группы ресурсов предоставляют средства для агрегирования ресурсов Azure, которыми можно управлять как группой.</span><span class="sxs-lookup"><span data-stu-id="eac4d-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="eac4d-145">Команда `az` вызывает [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="eac4d-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="eac4d-146">Интерфейс командной строки (CLI) можно запустить локально, но использование его в Cloud Shell экономит время и конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="eac4d-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="eac4d-147">В.</span><span class="sxs-lookup"><span data-stu-id="eac4d-147">c.</span></span> <span data-ttu-id="eac4d-148">Создайте план службы приложений уровня S1.</span><span class="sxs-lookup"><span data-stu-id="eac4d-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="eac4d-149">План службы приложений — это группа веб-приложений, использующих одну и ту же ценовую категорию.</span><span class="sxs-lookup"><span data-stu-id="eac4d-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="eac4d-150">Уровень S1 не является бесплатным, но он необходим для использования функции промежуточных слотов.</span><span class="sxs-lookup"><span data-stu-id="eac4d-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="eac4d-151">Г.</span><span class="sxs-lookup"><span data-stu-id="eac4d-151">d.</span></span> <span data-ttu-id="eac4d-152">Создайте ресурс веб-приложения с помощью плана службы приложений в той же группе ресурсов.</span><span class="sxs-lookup"><span data-stu-id="eac4d-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="eac4d-153">Д.</span><span class="sxs-lookup"><span data-stu-id="eac4d-153">e.</span></span> <span data-ttu-id="eac4d-154">Задайте учетные данные развертывания.</span><span class="sxs-lookup"><span data-stu-id="eac4d-154">Set the deployment credentials.</span></span> <span data-ttu-id="eac4d-155">Эти учетные данные развертывания применяются ко всем веб-приложениям в вашей подписке.</span><span class="sxs-lookup"><span data-stu-id="eac4d-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="eac4d-156">Не используйте в имени пользователя специальные символы.</span><span class="sxs-lookup"><span data-stu-id="eac4d-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="eac4d-157">f.</span><span class="sxs-lookup"><span data-stu-id="eac4d-157">f.</span></span> <span data-ttu-id="eac4d-158">Настройте веб-приложение на прием развертываний из локального репозитория Git и отобразите *URL-адрес развертывания Git*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="eac4d-159">**Запишите этот URL-адрес для дальнейшего использования**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="eac4d-160">ж.</span><span class="sxs-lookup"><span data-stu-id="eac4d-160">g.</span></span> <span data-ttu-id="eac4d-161">Отобразите *URL-адрес веб-приложения*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-161">Display the *web app URL*.</span></span> <span data-ttu-id="eac4d-162">Перейдите по этому URL-адресу — должно отобразиться пустое веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="eac4d-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="eac4d-163">**Запишите этот URL-адрес для дальнейшего использования**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="eac4d-164">Используя командную оболочку на локальном компьютере, перейдите к папке проекта веб-приложения (например, `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="eac4d-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="eac4d-165">Выполните следующие команды, чтобы настроить Git для отправки в URL-адрес развертывания.</span><span class="sxs-lookup"><span data-stu-id="eac4d-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="eac4d-166">1\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-166">a.</span></span> <span data-ttu-id="eac4d-167">Добавьте удаленный URL-адрес в локальный репозиторий.</span><span class="sxs-lookup"><span data-stu-id="eac4d-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="eac4d-168">2\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-168">b.</span></span> <span data-ttu-id="eac4d-169">Отправьте локальную ветвь *master* в удаленную ветвь *master* репозитория *azure-prod*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="eac4d-170">Вам будет предложено ввести учетные данные развертывания, созданные ранее.</span><span class="sxs-lookup"><span data-stu-id="eac4d-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="eac4d-171">Просмотрите выходные данные в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="eac4d-171">Observe the output in the command shell.</span></span> <span data-ttu-id="eac4d-172">Azure выполнит сборку приложения ASP.NET Core удаленно.</span><span class="sxs-lookup"><span data-stu-id="eac4d-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="eac4d-173">В браузере перейдите по *URL-адресу веб-приложения* и убедитесь, что приложение создано и развернуто.</span><span class="sxs-lookup"><span data-stu-id="eac4d-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="eac4d-174">Дополнительные изменения можно зафиксировать в локальном репозитории Git с помощью команды `git commit`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="eac4d-175">Эти изменения отправляются в Azure с помощью предыдущей команды `git push`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="eac4d-176">Развертывание с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eac4d-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="eac4d-177">*Примечание. Этот раздел относится только к Windows. Пользователи Linux и macOS должны внести изменения, описанные в шаге 2 ниже. Сохраните файл и зафиксируйте изменения в локальном репозитории с помощью команды `git commit`. Наконец, отправьте изменение с помощью команды `git push`, как описано в первом разделе.*</span><span class="sxs-lookup"><span data-stu-id="eac4d-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="eac4d-178">Приложение уже развернуто из командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="eac4d-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="eac4d-179">Давайте используем интегрированные средства Visual Studio для развертывания обновления в приложении.</span><span class="sxs-lookup"><span data-stu-id="eac4d-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="eac4d-180">По сути, Visual Studio выполняет ту же задачу, что и средства командной строки, но в привычном пользовательском интерфейсе Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eac4d-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="eac4d-181">Откройте файл *SimpleFeedReader.sln* в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eac4d-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="eac4d-182">В обозревателе решений откройте файл *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="eac4d-183">Измените `<h2>Simple Feed Reader</h2>` на `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="eac4d-184">Нажмите сочетание клавиш **CTRL**+**SHIFT**+**B** для сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="eac4d-185">В обозревателе решений щелкните правой кнопкой мыши проект и выберите **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Снимок экрана: щелчок правой кнопкой мыши, команда "Опубликовать"](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="eac4d-187">Visual Studio может создать новый ресурс службы приложений, но это обновление будет опубликовано в существующем развертывании.</span><span class="sxs-lookup"><span data-stu-id="eac4d-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="eac4d-188">В диалоговом окне **Выбор целевого объекта публикации** выберите в списке слева **Служба приложений**, а затем нажмите **Выбрать существующий**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="eac4d-189">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-189">Click **Publish**.</span></span>
6. <span data-ttu-id="eac4d-190">В диалоговом окне **Служба приложений** убедитесь, что в правом верхнем углу отображается учетная запись Майкрософт или организации, используемая для создания подписки Azure.</span><span class="sxs-lookup"><span data-stu-id="eac4d-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="eac4d-191">Если это не так, щелкните раскрывающийся список и добавьте ее.</span><span class="sxs-lookup"><span data-stu-id="eac4d-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="eac4d-192">Убедитесь, что выбрана правильная **подписка** Azure.</span><span class="sxs-lookup"><span data-stu-id="eac4d-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="eac4d-193">В поле **Вид** выберите **Группа ресурсов**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="eac4d-194">Разверните группу ресурсов **AzureTutorial**, а затем выберите существующее веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="eac4d-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="eac4d-195">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-195">Click **OK**.</span></span>

    ![Снимок экрана: диалоговое окно публикации службы приложений](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="eac4d-197">Visual Studio выполнит сборку и развертывание приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="eac4d-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="eac4d-198">Перейдите по URL-адресу приложения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-198">Browse to the web app URL.</span></span> <span data-ttu-id="eac4d-199">Проверьте, что произошло изменение элемента `<h2>`.</span><span class="sxs-lookup"><span data-stu-id="eac4d-199">Validate that the `<h2>` element modification is live.</span></span>

![Приложение с измененным названием](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="eac4d-201">Слоты развертывания</span><span class="sxs-lookup"><span data-stu-id="eac4d-201">Deployment slots</span></span>

<span data-ttu-id="eac4d-202">Слоты развертывания поддерживают промежуточное хранение изменений без влияния на приложение, выполняющееся в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="eac4d-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="eac4d-203">После того как промежуточная версия приложения будет проверена командой контроля качества, рабочий и промежуточный слоты можно поменять местами.</span><span class="sxs-lookup"><span data-stu-id="eac4d-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="eac4d-204">Таким образом, приложение в промежуточной среде повышается до рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="eac4d-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="eac4d-205">Следующие шаги описывают создание промежуточного слота, развертывание некоторых изменений и переключение промежуточного и рабочего слотов после проверки.</span><span class="sxs-lookup"><span data-stu-id="eac4d-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="eac4d-206">Войдите в [Azure Cloud Shell](https://shell.azure.com/bash), если еще не сделали этого.</span><span class="sxs-lookup"><span data-stu-id="eac4d-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="eac4d-207">Создайте промежуточный слот.</span><span class="sxs-lookup"><span data-stu-id="eac4d-207">Create the staging slot.</span></span>

    <span data-ttu-id="eac4d-208">1\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-208">a.</span></span> <span data-ttu-id="eac4d-209">Создайте слот развертывания с именем *staging*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="eac4d-210">2\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-210">b.</span></span> <span data-ttu-id="eac4d-211">Настройте промежуточный слот для использования развертывания из локального репозитория Git и получите URL-адрес развертывания **staging**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="eac4d-212">**Запишите этот URL-адрес для дальнейшего использования**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="eac4d-213">В.</span><span class="sxs-lookup"><span data-stu-id="eac4d-213">c.</span></span> <span data-ttu-id="eac4d-214">Отобразите URL-адрес промежуточного слота.</span><span class="sxs-lookup"><span data-stu-id="eac4d-214">Display the staging slot's URL.</span></span> <span data-ttu-id="eac4d-215">Перейдите по URL-адресу — должен отобразиться пустой промежуточный слот.</span><span class="sxs-lookup"><span data-stu-id="eac4d-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="eac4d-216">**Запишите этот URL-адрес для дальнейшего использования**.</span><span class="sxs-lookup"><span data-stu-id="eac4d-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="eac4d-217">В текстовом редакторе или в Visual Studio еще раз измените файл *Pages/Index.cshtml*, чтобы элемент `<h2>` стал выглядеть как `<h2>Simple Feed Reader - V3</h2>`, и сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="eac4d-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="eac4d-218">Зафиксируйте файл в локальном репозитории Git, используя страницу **Изменения** на вкладке *Team Explorer* в Visual Studio или с помощью следующей команды командной оболочки локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="eac4d-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="eac4d-219">С помощью командной оболочки локального компьютера добавьте URL-адрес промежуточного развертывания в качестве удаленного репозитория Git и отправьте зафиксированные изменения:</span><span class="sxs-lookup"><span data-stu-id="eac4d-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="eac4d-220">1\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-220">a.</span></span> <span data-ttu-id="eac4d-221">Добавьте удаленный URL-адрес для промежуточного хранения в локальный репозиторий Git.</span><span class="sxs-lookup"><span data-stu-id="eac4d-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="eac4d-222">2\.</span><span class="sxs-lookup"><span data-stu-id="eac4d-222">b.</span></span> <span data-ttu-id="eac4d-223">Отправьте локальную ветвь *master* в удаленную ветвь *master* репозитория *azure-staging*.</span><span class="sxs-lookup"><span data-stu-id="eac4d-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="eac4d-224">Подождите, пока Azure создаст и развернет приложение.</span><span class="sxs-lookup"><span data-stu-id="eac4d-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="eac4d-225">Чтобы убедиться, что версия 3 (V3) развернута в промежуточном слоте, откройте два окна браузера.</span><span class="sxs-lookup"><span data-stu-id="eac4d-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="eac4d-226">В одном окне перейдите к URL-адресу исходного веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="eac4d-227">В другом окне перейдите к URL-адресу промежуточного веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="eac4d-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="eac4d-228">Рабочий URL-адрес обслуживает приложение версии 2 (V2).</span><span class="sxs-lookup"><span data-stu-id="eac4d-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="eac4d-229">Промежуточный URL-адрес обслуживает приложение версии 3 (V3).</span><span class="sxs-lookup"><span data-stu-id="eac4d-229">The staging URL serves V3 of the app.</span></span>

    ![Снимок экрана: сравнение окон браузера](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="eac4d-231">В Cloud Shell замените проверенный и испытанный промежуточный слот на рабочий.</span><span class="sxs-lookup"><span data-stu-id="eac4d-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="eac4d-232">Убедитесь, что переключение произошло, обновив два окна браузера.</span><span class="sxs-lookup"><span data-stu-id="eac4d-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Сравнение окон браузера после переключения](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="eac4d-234">Сводка</span><span class="sxs-lookup"><span data-stu-id="eac4d-234">Summary</span></span>

<span data-ttu-id="eac4d-235">В этом разделе были выполнены следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="eac4d-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="eac4d-236">скачивание и создание примера приложения;</span><span class="sxs-lookup"><span data-stu-id="eac4d-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="eac4d-237">создание веб-приложения службы приложений Azure с помощью Azure Cloud Shell;</span><span class="sxs-lookup"><span data-stu-id="eac4d-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="eac4d-238">развертывание примера приложения в Azure;</span><span class="sxs-lookup"><span data-stu-id="eac4d-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="eac4d-239">развертывание изменения в приложении с помощью Visual Studio;</span><span class="sxs-lookup"><span data-stu-id="eac4d-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="eac4d-240">добавление в веб-приложение промежуточного слота;</span><span class="sxs-lookup"><span data-stu-id="eac4d-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="eac4d-241">развертывание обновления в промежуточном слоте;</span><span class="sxs-lookup"><span data-stu-id="eac4d-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="eac4d-242">переключение промежуточного и рабочего слотов.</span><span class="sxs-lookup"><span data-stu-id="eac4d-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="eac4d-243">В следующем разделе вы узнаете, как создать конвейер DevOps с помощью Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="eac4d-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="eac4d-244">Дополнительные материалы для чтения</span><span class="sxs-lookup"><span data-stu-id="eac4d-244">Additional reading</span></span>

* [<span data-ttu-id="eac4d-245">Обзор веб-приложений</span><span class="sxs-lookup"><span data-stu-id="eac4d-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="eac4d-246">Сборка веб-приложения .NET Core и базы данных SQL в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="eac4d-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="eac4d-247">Настройка учетных данных развертывания службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="eac4d-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="eac4d-248">Настройка промежуточных сред в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="eac4d-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
