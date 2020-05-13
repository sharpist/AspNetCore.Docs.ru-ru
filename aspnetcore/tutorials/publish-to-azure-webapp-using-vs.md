---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777089"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="db8dd-103">Публикация приложения ASP.NET Core в Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db8dd-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="db8dd-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="db8dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="db8dd-105">См. сведения о [публикации веб-приложения в Службе приложений Azure с помощью Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="db8dd-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="db8dd-106">Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="db8dd-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="db8dd-107">Настройка</span><span class="sxs-lookup"><span data-stu-id="db8dd-107">Set up</span></span>

* <span data-ttu-id="db8dd-108">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="db8dd-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="db8dd-109">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="db8dd-109">Create a web app</span></span>

<span data-ttu-id="db8dd-110">На начальной странице Visual Studio последовательно выберите **Файл > Создать > Проект…**</span><span class="sxs-lookup"><span data-stu-id="db8dd-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![меню "Файл"](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="db8dd-112">В диалоговом окне **Создание проекта** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="db8dd-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="db8dd-113">В левой области выберите **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="db8dd-114">В центральной области выберите **Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="db8dd-115">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-115">Select **OK**.</span></span>

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="db8dd-117">В диалоговом окне **Создание веб-приложения ASP.NET Core** сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="db8dd-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="db8dd-118">Выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-118">Select **Web Application**.</span></span>
* <span data-ttu-id="db8dd-119">Выберите **Изменить способ проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-119">Select **Change Authentication**.</span></span>

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="db8dd-121">Появится диалоговое окно **Изменение способа проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="db8dd-122">Выберите **Учетные записи отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="db8dd-123">Нажмите кнопку **ОК**, чтобы вернуться на страницу **Создание веб-приложения ASP.NET Core**, а затем снова нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Диалоговое окно "Новый способ веб-проверки подлинности ASP.NET Core"](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="db8dd-125">Visual Studio создает решение.</span><span class="sxs-lookup"><span data-stu-id="db8dd-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="db8dd-126">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="db8dd-126">Run the app</span></span>

* <span data-ttu-id="db8dd-127">Нажмите клавиши CTRL+F5, чтобы запустить проект.</span><span class="sxs-lookup"><span data-stu-id="db8dd-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="db8dd-128">Протестируйте ссылки **О программе** и **Контакт**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-128">Test the **About** and **Contact** links.</span></span>

![Веб-приложение откроется в localhost.](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="db8dd-130">Регистрация пользователя</span><span class="sxs-lookup"><span data-stu-id="db8dd-130">Register a user</span></span>

* <span data-ttu-id="db8dd-131">Выберите **Зарегистрироваться** и зарегистрируйте нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="db8dd-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="db8dd-132">Можно использовать вымышленный адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="db8dd-132">You can use a fictitious email address.</span></span> <span data-ttu-id="db8dd-133">После отправки на странице отображается следующая ошибка.</span><span class="sxs-lookup"><span data-stu-id="db8dd-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="db8dd-134">*"Внутренняя ошибка сервера: не удалось выполнить операцию базы данных при обработке запроса. Исключение SQL: невозможно открыть базу данных. Применение имеющихся миграций для контекста базы данных приложения, возможно, позволит решить проблему."*</span><span class="sxs-lookup"><span data-stu-id="db8dd-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="db8dd-135">Выберите **Применить миграции** и после обновления страницы перезагрузите ее.</span><span class="sxs-lookup"><span data-stu-id="db8dd-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Внутренняя ошибка сервера: не удалось выполнить операцию базы данных при обработке запроса.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="db8dd-139">Приложение отобразит адрес электронной почты, который использовался для регистрации нового пользователя, и ссылку **Выйти**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Веб-приложение откроется в Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="db8dd-142">Развертывание приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="db8dd-142">Deploy the app to Azure</span></span>

<span data-ttu-id="db8dd-143">В обозревателе решений щелкните правой кнопкой мыши проект и выберите команду **Опубликовать…**</span><span class="sxs-lookup"><span data-stu-id="db8dd-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="db8dd-145">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="db8dd-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="db8dd-146">Выберите **Служба приложений Microsoft Azure**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="db8dd-147">Выберите значок шестеренки и затем пункт **Создать профиль**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="db8dd-148">Выберите **Создать профиль**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-148">Select **Create Profile**.</span></span>

![Диалоговое окно публикации](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="db8dd-150">Создание ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="db8dd-150">Create Azure resources</span></span>

<span data-ttu-id="db8dd-151">Отображается диалоговое окно **Создание приложения службы**:</span><span class="sxs-lookup"><span data-stu-id="db8dd-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="db8dd-152">Введите свою подписку.</span><span class="sxs-lookup"><span data-stu-id="db8dd-152">Enter your subscription.</span></span>
* <span data-ttu-id="db8dd-153">Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="db8dd-154">Вы можете сохранить эти имена или изменить их.</span><span class="sxs-lookup"><span data-stu-id="db8dd-154">You can keep these names or change them.</span></span>

![Диалоговое окно "Служба приложений"](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="db8dd-156">Выберите вкладку **Службы**, чтобы создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="db8dd-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="db8dd-157">Выберите зеленый значок **+** , чтобы создать базу данных SQL.</span><span class="sxs-lookup"><span data-stu-id="db8dd-157">Select the green **+** icon to create a new SQL Database</span></span>

![Новая база данных SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="db8dd-159">Щелкните **Создать…** в диалоговом окне **Настройка базы данных SQL**, чтобы создать базу данных.</span><span class="sxs-lookup"><span data-stu-id="db8dd-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Новый сервер и база данных SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="db8dd-161">Появится диалоговое окно **Настройка SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="db8dd-162">Введите имя пользователя и пароль администратора, а затем нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="db8dd-163">Вы можете сохранить **Имя сервера** по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="db8dd-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="db8dd-164">"admin" запрещено использовать в качестве имени пользователя администратора.</span><span class="sxs-lookup"><span data-stu-id="db8dd-164">"admin" isn't allowed as the administrator user name.</span></span>

![Диалоговое окно "Настройка SQL Server"](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="db8dd-166">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-166">Select **OK**.</span></span>

<span data-ttu-id="db8dd-167">Visual Studio вернет диалоговое окно **Создание службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="db8dd-168">Нажмите кнопку **Создать** в диалоговом окне **Создание службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Диалоговое окно "Настройка базы данных SQL"](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="db8dd-170">Visual Studio создает веб-приложение и SQL Server в Azure.</span><span class="sxs-lookup"><span data-stu-id="db8dd-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="db8dd-171">Это может занять несколько минут.</span><span class="sxs-lookup"><span data-stu-id="db8dd-171">This step can take a few minutes.</span></span> <span data-ttu-id="db8dd-172">Сведения о созданных ресурсах см. в разделе [Дополнительные ресурсы](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="db8dd-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="db8dd-173">После завершения развертывания выберите **Параметры**:</span><span class="sxs-lookup"><span data-stu-id="db8dd-173">When deployment completes, select **Settings**:</span></span>

![Диалоговое окно "Настройка SQL Server"](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="db8dd-175">На странице **Параметры** диалогового окна **Публикация**</span><span class="sxs-lookup"><span data-stu-id="db8dd-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="db8dd-176">Разверните раздел **Базы данных** и установите флажок **Использовать эту строку подключения во время выполнения**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="db8dd-177">Разверните раздел **Миграции Entity Framework** и установите флажок **Использовать эту миграцию при публикации**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="db8dd-178">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-178">Select **Save**.</span></span> <span data-ttu-id="db8dd-179">Visual Studio вернется в диалоговое окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="db8dd-181">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-181">Click **Publish**.</span></span> <span data-ttu-id="db8dd-182">Visual Studio публикует приложение в Azure.</span><span class="sxs-lookup"><span data-stu-id="db8dd-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="db8dd-183">По завершении развертывания приложение открывается в браузере.</span><span class="sxs-lookup"><span data-stu-id="db8dd-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="db8dd-184">Тестирование приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="db8dd-184">Test your app in Azure</span></span>

* <span data-ttu-id="db8dd-185">Протестируйте ссылки **О программе** и **Контакт**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="db8dd-186">Регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="db8dd-186">Register a new user</span></span>

![Веб-приложение, открытое в Microsoft Edge в службе приложений Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="db8dd-188">Обновление приложения</span><span class="sxs-lookup"><span data-stu-id="db8dd-188">Update the app</span></span>

* <span data-ttu-id="db8dd-189">Измените страницу \*\* Pages/About.cshtmlRazor и ее содержимое.</span><span class="sxs-lookup"><span data-stu-id="db8dd-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="db8dd-190">Например, вы можете изменить абзац на "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="db8dd-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="db8dd-191">Щелкните правой кнопкой мыши проект и снова выберите пункт **Опубликовать...** .</span><span class="sxs-lookup"><span data-stu-id="db8dd-191">Right-click on the project and select **Publish...** again.</span></span>

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="db8dd-193">После публикации приложения убедитесь, что внесенные изменения доступны в Azure.</span><span class="sxs-lookup"><span data-stu-id="db8dd-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Убедитесь, что задача завершена.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="db8dd-195">Очистка</span><span class="sxs-lookup"><span data-stu-id="db8dd-195">Clean up</span></span>

<span data-ttu-id="db8dd-196">Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.</span><span class="sxs-lookup"><span data-stu-id="db8dd-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="db8dd-197">Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="db8dd-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="db8dd-199">На странице **Группы ресурсов** выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-199">In the **Resource groups** page, select **Delete**.</span></span>

![Портал Azure: страница "Группы ресурсов"](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="db8dd-201">Введите имя группы ресурсов и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="db8dd-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="db8dd-202">Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.</span><span class="sxs-lookup"><span data-stu-id="db8dd-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="db8dd-203">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="db8dd-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="db8dd-204">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="db8dd-204">Additional resources</span></span>

* <span data-ttu-id="db8dd-205">Сведения о Visual Studio Code см. в разделе [Профили публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="db8dd-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* <span data-ttu-id="db8dd-206">[служба приложений Azure](/azure/app-service/app-service-web-overview);</span><span class="sxs-lookup"><span data-stu-id="db8dd-206">[Azure App Service](/azure/app-service/app-service-web-overview)</span></span>
* [<span data-ttu-id="db8dd-207">Группа ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="db8dd-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="db8dd-208">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="db8dd-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
