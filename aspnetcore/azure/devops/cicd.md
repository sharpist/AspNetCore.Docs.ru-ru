---
title: Непрерывная интеграция и развертывание — DevOps с ASP.NET Core и Azure
author: CamSoper
description: Непрерывная интеграция и развертывание в DevOps с ASP.NET Core и Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901214"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="7863e-103">Непрерывная интеграция и развертывание</span><span class="sxs-lookup"><span data-stu-id="7863e-103">Continuous integration and deployment</span></span>

<span data-ttu-id="7863e-104">В предыдущей главе вы создали локальный репозиторий GIT для приложения Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="7863e-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="7863e-105">В этой главе вы опубликуете код в репозитории GitHub и создадите конвейер Azure DevOps Services с помощью Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="7863e-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="7863e-106">Конвейер обеспечивает непрерывную сборку и развертывание приложения.</span><span class="sxs-lookup"><span data-stu-id="7863e-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="7863e-107">Любая фиксация в репозитории GitHub активирует сборку и развертывание в промежуточном слоте веб-приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="7863e-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="7863e-108">В этом разделе вам предстоит выполнить следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="7863e-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="7863e-109">опубликовать код приложения в GitHub;</span><span class="sxs-lookup"><span data-stu-id="7863e-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="7863e-110">отключить локальное развертывание в GIT;</span><span class="sxs-lookup"><span data-stu-id="7863e-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="7863e-111">создать организацию Azure DevOps;</span><span class="sxs-lookup"><span data-stu-id="7863e-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="7863e-112">создать командный проект в Azure DevOps Services;</span><span class="sxs-lookup"><span data-stu-id="7863e-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="7863e-113">Создание определения построения</span><span class="sxs-lookup"><span data-stu-id="7863e-113">Create a build definition</span></span>
* <span data-ttu-id="7863e-114">Создание конвейера выпуска</span><span class="sxs-lookup"><span data-stu-id="7863e-114">Create a release pipeline</span></span>
* <span data-ttu-id="7863e-115">Фиксация изменений в GitHub и автоматическое развертывание в Azure</span><span class="sxs-lookup"><span data-stu-id="7863e-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="7863e-116">изучить конвейер Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="7863e-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="7863e-117">Публикация кода приложения в GitHub</span><span class="sxs-lookup"><span data-stu-id="7863e-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="7863e-118">Откройте окно браузера и перейдите по адресу `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="7863e-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="7863e-119">Щелкните стрелку раскрывающегося списка **+** в заголовке и выберите пункт **New repository** (Новый репозиторий).</span><span class="sxs-lookup"><span data-stu-id="7863e-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Пункт New Repository в GitHub](media/cicd/github-new-repo.png)

1. <span data-ttu-id="7863e-121">В раскрывающемся списке **Owner** (Владелец) выберите свою учетную запись, а затем в текстовом поле **Repository name** (Имя репозитория) введите *simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="7863e-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="7863e-122">Нажмите кнопку **Create repository** (Создать репозиторий).</span><span class="sxs-lookup"><span data-stu-id="7863e-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="7863e-123">Откройте командную оболочку на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="7863e-123">Open your local machine's command shell.</span></span> <span data-ttu-id="7863e-124">Перейдите в каталог, в котором хранится репозиторий GIT *simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="7863e-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="7863e-125">Переименуйте существующий удаленный репозиторий *origin* в *upstream*.</span><span class="sxs-lookup"><span data-stu-id="7863e-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="7863e-126">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7863e-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="7863e-127">Добавьте новый удаленный репозиторий *origin*, связанный с вашей копией репозитория в GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="7863e-128">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7863e-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="7863e-129">Опубликуйте локальный репозиторий GIT в только что созданном репозитории GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="7863e-130">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="7863e-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="7863e-131">Откройте окно браузера и перейдите по адресу `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="7863e-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="7863e-132">Проверьте, имеется ли ваш код в репозитории GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="7863e-133">Отключение локального развертывания в GIT</span><span class="sxs-lookup"><span data-stu-id="7863e-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="7863e-134">Удалите локальное развертывание GIT, выполнив указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="7863e-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="7863e-135">Azure Pipelines (служба Azure DevOps) заменяет его и дополняет его функциональные возможности.</span><span class="sxs-lookup"><span data-stu-id="7863e-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="7863e-136">На [портале Azure](https://portal.azure.com/) перейдите к *промежуточному веб-приложению (mywebapp\<unique_number\>/staging)* .</span><span class="sxs-lookup"><span data-stu-id="7863e-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="7863e-137">Его можно быстро найти, введя в поле поиска на портале запрос *промежуточное*.</span><span class="sxs-lookup"><span data-stu-id="7863e-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![Поиск промежуточного веб-приложения](media/cicd/portal-search-box.png)

1. <span data-ttu-id="7863e-139">Щелкните **Центр развертывания**.</span><span class="sxs-lookup"><span data-stu-id="7863e-139">Click **Deployment Center**.</span></span> <span data-ttu-id="7863e-140">Появится новая панель.</span><span class="sxs-lookup"><span data-stu-id="7863e-140">A new panel appears.</span></span> <span data-ttu-id="7863e-141">Щелкните **Отключить**, чтобы удалить локальную конфигурацию управления исходным кодом GIT, добавленную в предыдущей главе.</span><span class="sxs-lookup"><span data-stu-id="7863e-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="7863e-142">Подтвердите удаление, нажав кнопку **Да**.</span><span class="sxs-lookup"><span data-stu-id="7863e-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="7863e-143">Перейдите к службе приложений *mywebapp<уникальный_номер>* .</span><span class="sxs-lookup"><span data-stu-id="7863e-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="7863e-144">Напомним, что ее можно легко найти с помощью поля поиска на портале.</span><span class="sxs-lookup"><span data-stu-id="7863e-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="7863e-145">Щелкните **Центр развертывания**.</span><span class="sxs-lookup"><span data-stu-id="7863e-145">Click **Deployment Center**.</span></span> <span data-ttu-id="7863e-146">Появится новая панель.</span><span class="sxs-lookup"><span data-stu-id="7863e-146">A new panel appears.</span></span> <span data-ttu-id="7863e-147">Щелкните **Отключить**, чтобы удалить локальную конфигурацию управления исходным кодом GIT, добавленную в предыдущей главе.</span><span class="sxs-lookup"><span data-stu-id="7863e-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="7863e-148">Подтвердите удаление, нажав кнопку **Да**.</span><span class="sxs-lookup"><span data-stu-id="7863e-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="7863e-149">Создание организации Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="7863e-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="7863e-150">Откройте браузер и перейдите на [страницу создания организации Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="7863e-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="7863e-151">В текстовом поле **Выберите запоминающееся имя** введите уникальное имя, чтобы сформировать URL-адрес для доступа к организации Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="7863e-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="7863e-152">Установите переключатель в положение **Git**, так как код размещается в репозитории GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="7863e-153">Нажмите кнопку **Continue** (Продолжить).</span><span class="sxs-lookup"><span data-stu-id="7863e-153">Click the **Continue** button.</span></span> <span data-ttu-id="7863e-154">После короткого ожидания будут созданы учетная запись и командный проект с именем *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="7863e-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Страница создания организации Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="7863e-156">Откройте сообщение электронной почты с подтверждением того, что организация и проект Azure DevOps готовы к использованию.</span><span class="sxs-lookup"><span data-stu-id="7863e-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="7863e-157">Нажмите кнопку **Запустить проект**.</span><span class="sxs-lookup"><span data-stu-id="7863e-157">Click the **Start your project** button:</span></span>

    ![Кнопка "Запустить проект"](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="7863e-159">В браузере откроется страница с адресом *\<account_name\>.visualstudio.com*.</span><span class="sxs-lookup"><span data-stu-id="7863e-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="7863e-160">Щелкните ссылку *MyFirstProject*, чтобы начать настройку конвейера DevOps проекта.</span><span class="sxs-lookup"><span data-stu-id="7863e-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="7863e-161">Настройка конвейера Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="7863e-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="7863e-162">Процесс состоит из трех отдельных шагов.</span><span class="sxs-lookup"><span data-stu-id="7863e-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="7863e-163">Выполнив действия, описанные в следующих трех разделах, вы получите работающий конвейер DevOps.</span><span class="sxs-lookup"><span data-stu-id="7863e-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="7863e-164">Предоставление Azure DevOps доступа к репозиторию GitHub</span><span class="sxs-lookup"><span data-stu-id="7863e-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="7863e-165">Разверните меню-гармошку **или выполнить сборку кода из внешнего репозитория**.</span><span class="sxs-lookup"><span data-stu-id="7863e-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="7863e-166">Нажмите кнопку **Настроить сборку**.</span><span class="sxs-lookup"><span data-stu-id="7863e-166">Click the **Setup Build** button:</span></span>

    ![Кнопка "Настроить сборку"](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="7863e-168">В разделе **Выбор источника** выберите пункт **GitHub**.</span><span class="sxs-lookup"><span data-stu-id="7863e-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Выбор источника — GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="7863e-170">Чтобы Azure DevOps мог получить доступ к репозиторию GitHub, необходима авторизация.</span><span class="sxs-lookup"><span data-stu-id="7863e-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="7863e-171">В текстовом поле **Имя подключения** введите *Подключение к GitHub <имя_пользователя_GitHub>* .</span><span class="sxs-lookup"><span data-stu-id="7863e-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="7863e-172">Пример:</span><span class="sxs-lookup"><span data-stu-id="7863e-172">For example:</span></span>

    ![Имя подключения к GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="7863e-174">Если в учетной записи GitHub включена двухфакторная проверка подлинности, необходим личный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="7863e-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="7863e-175">В этом случае щелкните ссылку **Авторизовать с личным маркером доступа GitHub**.</span><span class="sxs-lookup"><span data-stu-id="7863e-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="7863e-176">За помощью обратитесь к [инструкциям по созданию личного маркера доступа GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).</span><span class="sxs-lookup"><span data-stu-id="7863e-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="7863e-177">Требуется только область разрешений *репозиторий*.</span><span class="sxs-lookup"><span data-stu-id="7863e-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="7863e-178">В противном случае нажмите кнопку **Авторизовать с помощью OAuth**.</span><span class="sxs-lookup"><span data-stu-id="7863e-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="7863e-179">При появлении запроса выполните вход в учетную запись GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="7863e-180">Затем выберите "Авторизовать", чтобы предоставить доступ к вашей организации Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="7863e-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="7863e-181">В случае успеха будет создана новая конечная точка службы.</span><span class="sxs-lookup"><span data-stu-id="7863e-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="7863e-182">Нажмите кнопку с многоточием рядом с кнопкой **Репозиторий**.</span><span class="sxs-lookup"><span data-stu-id="7863e-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="7863e-183">Выберите в списке репозиторий *<имя_пользователя_GitHub>/simple-feed-reader*.</span><span class="sxs-lookup"><span data-stu-id="7863e-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="7863e-184">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="7863e-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="7863e-185">В раскрывающемся списке **Ветвь по умолчанию для сборок вручную и по расписанию** выберите ветвь по умолчанию (*master*).</span><span class="sxs-lookup"><span data-stu-id="7863e-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="7863e-186">Нажмите кнопку **Continue** (Продолжить).</span><span class="sxs-lookup"><span data-stu-id="7863e-186">Click the **Continue** button.</span></span> <span data-ttu-id="7863e-187">Откроется страница выбора шаблона.</span><span class="sxs-lookup"><span data-stu-id="7863e-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="7863e-188">Создание определения сборки</span><span class="sxs-lookup"><span data-stu-id="7863e-188">Create the build definition</span></span>

1. <span data-ttu-id="7863e-189">На странице выбора шаблона в поле поиска введите *ASP.NET Core*.</span><span class="sxs-lookup"><span data-stu-id="7863e-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![Поиск ASP.NET Core на странице выбора шаблона](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="7863e-191">Появятся результаты поиска шаблона.</span><span class="sxs-lookup"><span data-stu-id="7863e-191">The template search results appear.</span></span> <span data-ttu-id="7863e-192">Наведите указатель на шаблон **ASP.NET Core** и нажмите кнопку **Применить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="7863e-193">Появится вкладка **Задачи** определения сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="7863e-194">Перейдите на вкладку **Триггеры** .</span><span class="sxs-lookup"><span data-stu-id="7863e-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="7863e-195">Установите флажок **Включить непрерывную интеграцию**.</span><span class="sxs-lookup"><span data-stu-id="7863e-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="7863e-196">В разделе **Фильтры ветвей** убедитесь в том, что в раскрывающемся списке **Тип** выбран пункт *Включить*.</span><span class="sxs-lookup"><span data-stu-id="7863e-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="7863e-197">В раскрывающемся списке **Спецификация ветви** выберите пункт *главная*.</span><span class="sxs-lookup"><span data-stu-id="7863e-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Параметры включения непрерывной интеграции](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="7863e-199">В результате установки этих параметров сборка будет происходить при отправке любого изменения в ветвь по умолчанию (*master*) репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="7863e-200">Тестирование непрерывной интеграции производится в разделе [Фиксация изменений в GitHub и автоматическое развертывание в Azure](#commit-changes-to-github-and-automatically-deploy-to-azure).</span><span class="sxs-lookup"><span data-stu-id="7863e-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="7863e-201">Нажмите кнопку **Сохранить и поместить в очередь** и выберите пункт **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Кнопка "Сохранить"](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="7863e-203">Появится следующее модальное диалоговое окно:</span><span class="sxs-lookup"><span data-stu-id="7863e-203">The following modal dialog appears:</span></span>

    ![Сохранение определения сборки — модальное диалоговое окно](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="7863e-205">Используйте папку по умолчанию *\\* и нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="7863e-206">Создание конвейера выпуска</span><span class="sxs-lookup"><span data-stu-id="7863e-206">Create the release pipeline</span></span>

1. <span data-ttu-id="7863e-207">Перейдите на вкладку **Выпуски** командного проекта.</span><span class="sxs-lookup"><span data-stu-id="7863e-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="7863e-208">Нажмите кнопку **Новый конвейер**.</span><span class="sxs-lookup"><span data-stu-id="7863e-208">Click the **New pipeline** button.</span></span>

    ![Вкладка "Выпуски" — кнопка "Новый конвейер"](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="7863e-210">Появится область выбора шаблона.</span><span class="sxs-lookup"><span data-stu-id="7863e-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="7863e-211">На странице выбора шаблона в поле поиска введите *Служба приложений*.</span><span class="sxs-lookup"><span data-stu-id="7863e-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Поле для поиска шаблона конвейера выпуска](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="7863e-213">Появятся результаты поиска шаблона.</span><span class="sxs-lookup"><span data-stu-id="7863e-213">The template search results appear.</span></span> <span data-ttu-id="7863e-214">Наведите указатель на шаблон **Развертывание службы приложений Azure с помощью слота** и нажмите кнопку **Применить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="7863e-215">Откроется вкладка **Конвейер** конвейера выпуска.</span><span class="sxs-lookup"><span data-stu-id="7863e-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Вкладка "Конвейер" конвейера выпуска](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="7863e-217">В поле **Артефакты** нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="7863e-218">Появится панель **Добавление артефакта**.</span><span class="sxs-lookup"><span data-stu-id="7863e-218">The **Add artifact** panel appears:</span></span>

    ![Конвейер выпуска — панель "Добавление артефакта"](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="7863e-220">В разделе **Тип источника** выберите плитку **Сборка**.</span><span class="sxs-lookup"><span data-stu-id="7863e-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="7863e-221">Этот тип позволяет связать конвейер выпуска с определением сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="7863e-222">В раскрывающемся списке **Проект** выберите пункт *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="7863e-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="7863e-223">В раскрывающемся списке **Источник (определение сборки)** выберите имя определения сборки *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="7863e-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="7863e-224">В раскрывающемся списке **Версия по умолчанию** выберите пункт *Последняя*.</span><span class="sxs-lookup"><span data-stu-id="7863e-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="7863e-225">В результате будет выполнена сборка артефактов, полученных при последнем запуске определения сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="7863e-226">Замените текст в поле **Псевдоним источника** на *Сброс*.</span><span class="sxs-lookup"><span data-stu-id="7863e-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="7863e-227">Нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-227">Click the **Add** button.</span></span> <span data-ttu-id="7863e-228">Содержимое в разделе **Артефакты** обновится в соответствии с изменениями.</span><span class="sxs-lookup"><span data-stu-id="7863e-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="7863e-229">Чтобы включить непрерывное развертывание, щелкните значок молнии.</span><span class="sxs-lookup"><span data-stu-id="7863e-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Артефакты конвейера выпуска — значок молнии](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="7863e-231">При включении этого параметра развертывание происходит каждый раз, когда становится доступна новая сборка.</span><span class="sxs-lookup"><span data-stu-id="7863e-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="7863e-232">Справа появится панель **Триггер непрерывного развертывания**.</span><span class="sxs-lookup"><span data-stu-id="7863e-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="7863e-233">Щелкните выключатель, чтобы включить эту функцию.</span><span class="sxs-lookup"><span data-stu-id="7863e-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="7863e-234">Включать **триггер запроса на вытягивание** необязательно.</span><span class="sxs-lookup"><span data-stu-id="7863e-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="7863e-235">В разделе **Фильтры ветви сборки** откройте раскрывающийся список **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="7863e-236">Выберите пункт **Ветвь по умолчанию в определении сборки**.</span><span class="sxs-lookup"><span data-stu-id="7863e-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="7863e-237">При применении этого фильтра выпуск активируется только для сборки из ветви по умолчанию (*master*) репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="7863e-238">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-238">Click the **Save** button.</span></span> <span data-ttu-id="7863e-239">В появившемся модальном диалоговом окне **Сохранить** нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7863e-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="7863e-240">Щелкните поле **Среда 1**.</span><span class="sxs-lookup"><span data-stu-id="7863e-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="7863e-241">Справа появится панель **Среда**.</span><span class="sxs-lookup"><span data-stu-id="7863e-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="7863e-242">Измените текст *Среда 1* в текстовом поле **Имя среды** на *Рабочая*.</span><span class="sxs-lookup"><span data-stu-id="7863e-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Конвейер выпуска — текстовое поле "Имя среды"](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="7863e-244">Щелкните ссылку **1 этап, 2 задачи** в поле **Рабочая**.</span><span class="sxs-lookup"><span data-stu-id="7863e-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Конвейер выпуска — ссылка на рабочую среду](media/cicd/vsts-production-link.png)

    <span data-ttu-id="7863e-246">Появится вкладка **Задачи** среды.</span><span class="sxs-lookup"><span data-stu-id="7863e-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="7863e-247">Выберите задачу **Развертывание службы приложений Azure в слоте**.</span><span class="sxs-lookup"><span data-stu-id="7863e-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="7863e-248">Ее параметры появятся на панели справа.</span><span class="sxs-lookup"><span data-stu-id="7863e-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="7863e-249">В раскрывающемся списке **Подписка Azure** выберите подписку Azure, связанную со службой приложений.</span><span class="sxs-lookup"><span data-stu-id="7863e-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="7863e-250">После этого нажмите кнопку **Авторизовать**.</span><span class="sxs-lookup"><span data-stu-id="7863e-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="7863e-251">В раскрывающемся списке **Тип приложения** выберите пункт *Веб-приложение*.</span><span class="sxs-lookup"><span data-stu-id="7863e-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="7863e-252">В раскрывающемся списке **Имя службы приложений** выберите пункт *mywebapp/<уникальный_номер/>* .</span><span class="sxs-lookup"><span data-stu-id="7863e-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="7863e-253">В раскрывающемся списке **Группа ресурсов** выберите пункт *AzureTutorial*.</span><span class="sxs-lookup"><span data-stu-id="7863e-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="7863e-254">В раскрывающемся списке **Слот** выберите пункт *Промежуточный*.</span><span class="sxs-lookup"><span data-stu-id="7863e-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="7863e-255">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="7863e-256">Наведите указатель на имя конвейера выпуска по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7863e-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="7863e-257">Щелкните значок карандаша, чтобы изменить его.</span><span class="sxs-lookup"><span data-stu-id="7863e-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="7863e-258">Используйте имя *MyFirstProject-ASP.NET Core-CD*.</span><span class="sxs-lookup"><span data-stu-id="7863e-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Имя конвейера выпуска](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="7863e-260">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7863e-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="7863e-261">Фиксация изменений в GitHub и автоматическое развертывание в Azure</span><span class="sxs-lookup"><span data-stu-id="7863e-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="7863e-262">Откройте файл *SimpleFeedReader.sln* в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7863e-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="7863e-263">В обозревателе решений откройте файл *Pages\Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7863e-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="7863e-264">Измените `<h2>Simple Feed Reader - V3</h2>` на `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="7863e-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="7863e-265">Нажмите сочетание клавиш **CTRL**+**SHIFT**+**B** для сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="7863e-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="7863e-266">Зафиксируйте файл в репозитории GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="7863e-267">Используйте страницу **Изменения** на вкладке *Team Explorer* в Visual Studio или выполните следующую команду в командной оболочке локального компьютера:</span><span class="sxs-lookup"><span data-stu-id="7863e-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="7863e-268">Отправьте изменение в ветвь по умолчанию (*master*) удаленного репозитория *origin* в GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="7863e-269">В следующей команде вместо заполнители `{BRANCH}` укажите ветвь по умолчанию (используйте `master`):</span><span class="sxs-lookup"><span data-stu-id="7863e-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="7863e-270">Фиксация появится в ветви по умолчанию (*master*) репозитория GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![Фиксация в ветви по умолчанию (master) GitHub](media/cicd/github-commit.png)

    <span data-ttu-id="7863e-272">Запустится сборка, так как на вкладке **Триггеры** определения сборки включена непрерывная интеграция.</span><span class="sxs-lookup"><span data-stu-id="7863e-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![включение непрерывной интеграции](media/cicd/enable-ci.png)

1. <span data-ttu-id="7863e-274">Перейдите на вкладку **В очереди** страницы **Azure Pipelines** > **Сборки** в Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="7863e-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="7863e-275">Для сборки в очереди показаны ветвь и фиксация, активировавшая сборку.</span><span class="sxs-lookup"><span data-stu-id="7863e-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![сборка в очереди](media/cicd/build-queued.png)

1. <span data-ttu-id="7863e-277">После успешной сборки производится развертывание в Azure.</span><span class="sxs-lookup"><span data-stu-id="7863e-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="7863e-278">Перейдите к приложению в браузере.</span><span class="sxs-lookup"><span data-stu-id="7863e-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="7863e-279">Обратите внимание, что в заголовке имеется текст "V4".</span><span class="sxs-lookup"><span data-stu-id="7863e-279">Notice that the "V4" text appears in the heading:</span></span>

    ![обновленное приложение](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="7863e-281">Изучение конвейера Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="7863e-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="7863e-282">Определение сборки</span><span class="sxs-lookup"><span data-stu-id="7863e-282">Build definition</span></span>

<span data-ttu-id="7863e-283">Определение сборки было создано с именем *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="7863e-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="7863e-284">После завершения сборки создается файл *ZIP*, содержащий ресурсы для публикации.</span><span class="sxs-lookup"><span data-stu-id="7863e-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="7863e-285">Конвейер выпуска развертывает эти ресурсы в Azure.</span><span class="sxs-lookup"><span data-stu-id="7863e-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="7863e-286">На вкладке **Задачи** определения сборки перечислены выполняемые шаги.</span><span class="sxs-lookup"><span data-stu-id="7863e-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="7863e-287">Имеется пять задач сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-287">There are five build tasks.</span></span>

![задачи определения сборки](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="7863e-289">**Восстановление** &mdash; выполняет команду `dotnet restore` для восстановления пакетов NuGet приложения.</span><span class="sxs-lookup"><span data-stu-id="7863e-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="7863e-290">По умолчанию используется веб-канал пакетов nuget.org.</span><span class="sxs-lookup"><span data-stu-id="7863e-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="7863e-291">**Сборка** &mdash; выполняет команду `dotnet build --configuration release` для компиляции кода приложения.</span><span class="sxs-lookup"><span data-stu-id="7863e-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="7863e-292">Параметр `--configuration` служит для создания оптимизированной версии кода, которая подходит для развертывания в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="7863e-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="7863e-293">Измените переменную *BuildConfiguration* на вкладке **Переменные** определения сборки, если, например, требуется конфигурация отладки.</span><span class="sxs-lookup"><span data-stu-id="7863e-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="7863e-294">**Тестирование** &mdash; выполняет команду `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` для выполнения модульных тестов приложения.</span><span class="sxs-lookup"><span data-stu-id="7863e-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="7863e-295">Модульные тесты выполняются в любом проекте C#, соответствующем стандартной маске `**/*Tests/*.csproj`.</span><span class="sxs-lookup"><span data-stu-id="7863e-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="7863e-296">Результаты тестов сохраняются в файле *TRX* в расположении, указанном с помощью параметра `--results-directory`.</span><span class="sxs-lookup"><span data-stu-id="7863e-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="7863e-297">Если какие-либо тесты завершаются неудачно, сборка завершается сбоем и не развертывается.</span><span class="sxs-lookup"><span data-stu-id="7863e-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7863e-298">Чтобы проверить работу модульных тестов, внесите в файл *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* изменение, нарушающее выполнение одного из них.</span><span class="sxs-lookup"><span data-stu-id="7863e-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="7863e-299">Например, измените `Assert.True(result.Count > 0);` на `Assert.False(result.Count > 0);` в методе `Returns_News_Stories_Given_Valid_Uri`.</span><span class="sxs-lookup"><span data-stu-id="7863e-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="7863e-300">Зафиксируйте изменение и отправьте его в GitHub.</span><span class="sxs-lookup"><span data-stu-id="7863e-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="7863e-301">Сборка запустится и завершится сбоем.</span><span class="sxs-lookup"><span data-stu-id="7863e-301">The build is triggered and fails.</span></span> <span data-ttu-id="7863e-302">Состояние конвейера сборки изменится на **сбой**.</span><span class="sxs-lookup"><span data-stu-id="7863e-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="7863e-303">Отмените изменение и снова выполните фиксацию и отправку.</span><span class="sxs-lookup"><span data-stu-id="7863e-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="7863e-304">Сборка завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="7863e-304">The build succeeds.</span></span>

1. <span data-ttu-id="7863e-305">**Публикация** &mdash; выполняет команду `dotnet publish --configuration release --output <local_path_on_build_agent>` для создания файла *ZIP* с развертываемыми артефактами.</span><span class="sxs-lookup"><span data-stu-id="7863e-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="7863e-306">Параметр `--output` указывает расположение публикации файла *ZIP*.</span><span class="sxs-lookup"><span data-stu-id="7863e-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="7863e-307">Это расположение задается путем передачи [предварительно определенной переменной](/azure/devops/pipelines/build/variables) с именем `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="7863e-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="7863e-308">Эта переменная развертывается в локальный путь, например *c:\agent\_work\1\a*, в агенте сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="7863e-309">**Публикация артефакта** &mdash; публикует файл *ZIP*, созданный в результате выполнения задачи **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="7863e-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="7863e-310">В качестве параметра принимается расположение файла *ZIP* в виде предварительно определенной переменной `$(build.artifactstagingdirectory)`.</span><span class="sxs-lookup"><span data-stu-id="7863e-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="7863e-311">Файл *ZIP* публикуется в виде папки *drop*.</span><span class="sxs-lookup"><span data-stu-id="7863e-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="7863e-312">Щелкните ссылку **Сводка** для определения сборки, чтобы просмотреть журнал сборок с этим определением.</span><span class="sxs-lookup"><span data-stu-id="7863e-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Снимок экрана: журнал определения сборки](media/cicd/build-definition-summary.png)

<span data-ttu-id="7863e-314">На открывшейся странице щелкните ссылку, соответствующую уникальному номеру сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Снимок экрана: страница сводки по определению сборки](media/cicd/build-definition-completed.png)

<span data-ttu-id="7863e-316">Отобразится сводка по данной сборке.</span><span class="sxs-lookup"><span data-stu-id="7863e-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="7863e-317">Перейдите на вкладку **Артефакты** и обратите внимание на наличие в списке папки *drop*, созданной в результате сборки.</span><span class="sxs-lookup"><span data-stu-id="7863e-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Снимок экрана: папка drop в списке артефактов определения сборки](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="7863e-319">Чтобы просмотреть опубликованные артефакты, воспользуйтесь ссылками **Скачать** и **Обзор**.</span><span class="sxs-lookup"><span data-stu-id="7863e-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="7863e-320">Конвейер выпуска</span><span class="sxs-lookup"><span data-stu-id="7863e-320">Release pipeline</span></span>

<span data-ttu-id="7863e-321">Конвейер сборки был создан с именем *MyFirstProject-ASP.NET Core-CD*.</span><span class="sxs-lookup"><span data-stu-id="7863e-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Снимок экрана: обзор конвейера выпуска](media/cicd/release-definition-overview.png)

<span data-ttu-id="7863e-323">Два основных компонента конвейера выпуска — это **артефакты** и **среды**.</span><span class="sxs-lookup"><span data-stu-id="7863e-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="7863e-324">Если щелкнуть поле в разделе **Артефакты**, появится следующая панель:</span><span class="sxs-lookup"><span data-stu-id="7863e-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Снимок экрана: артефакты конвейера выпуска](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="7863e-326">В поле **Источник (определение сборки)** указано определение сборки, с которым связан этот конвейер выпуска.</span><span class="sxs-lookup"><span data-stu-id="7863e-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="7863e-327">Файл *ZIP*, созданный в результате успешного выполнения определения сборки, передается в *рабочую* среду для развертывания в Azure.</span><span class="sxs-lookup"><span data-stu-id="7863e-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="7863e-328">Щелкните ссылку *1 этап, 2 задачи* в поле *Рабочая*, чтобы просмотреть задачи конвейера выпуска.</span><span class="sxs-lookup"><span data-stu-id="7863e-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Снимок экрана: задачи конвейера выпуска](media/cicd/release-definition-tasks.png)

<span data-ttu-id="7863e-330">Конвейер выпуска состоит из двух задач: *Развертывание службы приложений Azure в слоте* и *Управление Службой приложений Azure — переключение слотов*.</span><span class="sxs-lookup"><span data-stu-id="7863e-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="7863e-331">Если щелкнуть первую задачу, появится следующая конфигурация:</span><span class="sxs-lookup"><span data-stu-id="7863e-331">Clicking the first task reveals the following task configuration:</span></span>

![Снимок экрана: задача развертывания конвейера выпуска](media/cicd/release-definition-task1.png)

<span data-ttu-id="7863e-333">В задаче развертывания определены подписка Azure, тип службы, имя веб-приложения, группа ресурсов и слот развертывания.</span><span class="sxs-lookup"><span data-stu-id="7863e-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="7863e-334">В текстовом поле **Пакет или папка** указывается путь к файлу *ZIP*, который следует извлечь и развернуть в *промежуточном* слоте веб-приложения *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="7863e-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="7863e-335">Если щелкнуть задачу переключения слотов, появится следующая конфигурация:</span><span class="sxs-lookup"><span data-stu-id="7863e-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![Снимок экрана: задача переключения слотов конвейера выпуска](media/cicd/release-definition-task2.png)

<span data-ttu-id="7863e-337">Приводятся сведения о подписке, группе ресурсов, типе службы, имени веб-приложения и слоте развертывания.</span><span class="sxs-lookup"><span data-stu-id="7863e-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="7863e-338">Флажок **Переключить на рабочий слот** установлен.</span><span class="sxs-lookup"><span data-stu-id="7863e-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="7863e-339">Соответственно код, развернутый в *промежуточном* слоте, переносится в рабочую среду.</span><span class="sxs-lookup"><span data-stu-id="7863e-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="7863e-340">Дополнительные материалы для чтения</span><span class="sxs-lookup"><span data-stu-id="7863e-340">Additional reading</span></span>

* [<span data-ttu-id="7863e-341">Создание первого конвейера с помощью Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="7863e-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="7863e-342">Сборка проекта .NET Core</span><span class="sxs-lookup"><span data-stu-id="7863e-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="7863e-343">Развертывание веб-приложения с помощью Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="7863e-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
