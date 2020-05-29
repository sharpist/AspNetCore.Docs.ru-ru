---
<span data-ttu-id="6f76b-101">название: автор: описание: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6f76b-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6f76b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6f76b-102">'Blazor'</span></span>
- <span data-ttu-id="6f76b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6f76b-103">'Identity'</span></span>
- <span data-ttu-id="6f76b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6f76b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6f76b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6f76b-105">'Razor'</span></span>
- <span data-ttu-id="6f76b-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="6f76b-106">'SignalR' uid:</span></span> 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="6f76b-107">Публикация приложения ASP.NET Core в Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-107">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="6f76b-108">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="6f76b-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="6f76b-109">См. сведения о [публикации веб-приложения в Службе приложений Azure с помощью Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019).</span><span class="sxs-lookup"><span data-stu-id="6f76b-109">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="6f76b-110">Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="6f76b-110">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="6f76b-111">Настройка</span><span class="sxs-lookup"><span data-stu-id="6f76b-111">Set up</span></span>

* <span data-ttu-id="6f76b-112">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="6f76b-112">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="6f76b-113">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="6f76b-113">Create a web app</span></span>

<span data-ttu-id="6f76b-114">На начальной странице Visual Studio последовательно выберите **Файл > Создать > Проект…**</span><span class="sxs-lookup"><span data-stu-id="6f76b-114">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![меню "Файл"](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="6f76b-116">В диалоговом окне **Создание проекта** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="6f76b-116">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="6f76b-117">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-117">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="6f76b-118">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-118">Select **Next**.</span></span>

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="6f76b-120">В диалоговом окне **Создание веб-приложения ASP.NET Core** сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="6f76b-120">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="6f76b-121">Выберите **Веб-приложение**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-121">Select **Web Application**.</span></span>
* <span data-ttu-id="6f76b-122">Выберите **Изменить** в разделе способа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6f76b-122">Select **Change** under Authentication.</span></span>

![Диалоговое окно нового веб-приложения ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="6f76b-124">Появится диалоговое окно **Изменение способа проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-124">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="6f76b-125">Выберите **Учетные записи отдельных пользователей**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-125">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="6f76b-126">Нажмите кнопку **ОК**, чтобы вернуться на страницу **Создать веб-приложение ASP.NET Core**, и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-126">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Диалоговое окно "Новый способ веб-проверки подлинности ASP.NET Core"](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="6f76b-128">Visual Studio создает решение.</span><span class="sxs-lookup"><span data-stu-id="6f76b-128">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6f76b-129">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="6f76b-129">Run the app</span></span>

* <span data-ttu-id="6f76b-130">Нажмите клавиши CTRL+F5, чтобы запустить проект.</span><span class="sxs-lookup"><span data-stu-id="6f76b-130">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="6f76b-131">Прочтите, что написано по ссылке **Конфиденциальность**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-131">Test the **Privacy** link.</span></span>

![Веб-приложение откроется в localhost.](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="6f76b-133">Регистрация пользователя</span><span class="sxs-lookup"><span data-stu-id="6f76b-133">Register a user</span></span>

* <span data-ttu-id="6f76b-134">Выберите **Зарегистрироваться** и зарегистрируйте нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="6f76b-134">Select **Register** and register a new user.</span></span> <span data-ttu-id="6f76b-135">Можно использовать вымышленный адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="6f76b-135">You can use a fictitious email address.</span></span> <span data-ttu-id="6f76b-136">После отправки на странице отображается следующая ошибка.</span><span class="sxs-lookup"><span data-stu-id="6f76b-136">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="6f76b-137">*Не удалось выполнить операцию базы данных при обработке запроса. Применение имеющихся миграций для контекста базы данных приложения, возможно, позволит решить проблему."*</span><span class="sxs-lookup"><span data-stu-id="6f76b-137">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="6f76b-138">Выберите **Применить миграции** и после обновления страницы перезагрузите ее.</span><span class="sxs-lookup"><span data-stu-id="6f76b-138">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![не удалось выполнить операцию базы данных при обработке запроса.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="6f76b-141">Приложение отобразит адрес электронной почты, который использовался для регистрации нового пользователя, и ссылку **Выйти**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-141">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Веб-приложение откроется в Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="6f76b-144">Развертывание приложения в Azure</span><span class="sxs-lookup"><span data-stu-id="6f76b-144">Deploy the app to Azure</span></span>

<span data-ttu-id="6f76b-145">В обозревателе решений щелкните правой кнопкой мыши проект и выберите команду **Опубликовать…**</span><span class="sxs-lookup"><span data-stu-id="6f76b-145">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="6f76b-147">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="6f76b-148">Выберите **Azure**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-148">Select **Azure**.</span></span>
* <span data-ttu-id="6f76b-149">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-149">Select **Next**.</span></span>

![Диалоговое окно публикации](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="6f76b-151">В диалоговом окне **Публикация**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-151">In the **Publish** dialog:</span></span>

* <span data-ttu-id="6f76b-152">Выберите **Служба приложений Azure (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="6f76b-152">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="6f76b-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-153">Select **Next**.</span></span>

![Диалоговое окно публикации: выбор службы Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="6f76b-155">В диалоговом окне **Публикация** выберите вариант **Create a new Azure App Service...** (Создать экземпляр Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="6f76b-155">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="6f76b-157">Отображается диалоговое окно **Создание приложения службы**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-157">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="6f76b-158">Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-158">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="6f76b-159">Вы можете сохранить эти имена или изменить их.</span><span class="sxs-lookup"><span data-stu-id="6f76b-159">You can keep these names or change them.</span></span>
* <span data-ttu-id="6f76b-160">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-160">Select **Create**.</span></span>

![Диалоговое окно "Создание службы приложений"](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="6f76b-162">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-162">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="6f76b-163">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="6f76b-163">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="6f76b-164">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-164">Select **Finish**.</span></span>

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="6f76b-166">Затем отобразится страница **Сводка** для профиля публикации.</span><span class="sxs-lookup"><span data-stu-id="6f76b-166">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="6f76b-167">Программа Visual Studio обнаружила, что для этого приложения требуется база данных SQL Server, и предлагает вам настроить ее.</span><span class="sxs-lookup"><span data-stu-id="6f76b-167">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="6f76b-168">Нажмите кнопку **Настроить**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-168">Select **Configure**.</span></span>

![Страница со сводными данными о профиле публикации: настройка зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="6f76b-170">Откроется диалоговое окно **Configure dependency** (Настройка зависимостей).</span><span class="sxs-lookup"><span data-stu-id="6f76b-170">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="6f76b-171">Выберите **База данных SQL Azure**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-171">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="6f76b-172">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-172">Select **Next**.</span></span>

![Диалоговое окно для настройки зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="6f76b-174">В диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) выберите **Создать базу данных SQL**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-174">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="6f76b-176">Появится окно **Create Azure SQL Database** (Создание базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="6f76b-176">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="6f76b-177">В нем уже заполнены поля **Имя базы данных**, **Группа ресурсов**, **Сервер базы данных** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-177">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="6f76b-178">Вы можете сохранить эти значения или изменить их.</span><span class="sxs-lookup"><span data-stu-id="6f76b-178">You can keep these values or change them.</span></span>
* <span data-ttu-id="6f76b-179">Заполните поля **Database administrator username** (Имя администратора базы данных) и **Пароль администратора базы данных** для выбранного параметра **Сервер базы данных** . (Обратите внимание, что у используемой учетной записи должны быть разрешения на создание базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="6f76b-179">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="6f76b-180">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-180">Select **Create**.</span></span>

![Диалоговое окно создания базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="6f76b-182">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Configure Azure SQL Database** (Настройка базы данных SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="6f76b-182">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="6f76b-183">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="6f76b-183">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="6f76b-184">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-184">Select **Next**.</span></span>

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="6f76b-186">На следующем шаге в диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="6f76b-186">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="6f76b-187">Заполните поля **Database connection user name** (Имя пользователя для подключения к базе данных) и **Database connection password** (Пароль для подключения к базе данных).</span><span class="sxs-lookup"><span data-stu-id="6f76b-187">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="6f76b-188">Эти сведения ваше приложение будет использовать при подключении к базе данных во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="6f76b-188">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="6f76b-189">Рекомендуется не указывать такое же имя администратора и пароль, которые вы использовали на предыдущем шаге.</span><span class="sxs-lookup"><span data-stu-id="6f76b-189">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="6f76b-190">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-190">Select **Finish**.</span></span>

![Диалоговое окно с настройками Базы данных SQL Azure, сведения о строке подключения](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="6f76b-192">На странице **Сводка** для профиля публикации выберите **Параметры**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-192">In the **Publish Profile summary** page select **Settings**:</span></span>

![Страница со сводными данными о профиле публикации: выбор параметров](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="6f76b-194">На странице **Параметры** диалогового окна **Публикация**</span><span class="sxs-lookup"><span data-stu-id="6f76b-194">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="6f76b-195">Разверните раздел **Базы данных** и установите флажок **Использовать эту строку подключения во время выполнения**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-195">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="6f76b-196">Разверните раздел **Миграции Entity Framework** и установите флажок **Использовать эту миграцию при публикации**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-196">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="6f76b-197">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-197">Select **Save**.</span></span> <span data-ttu-id="6f76b-198">Visual Studio вернется в диалоговое окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-198">Visual Studio returns to the **Publish** dialog.</span></span> 

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="6f76b-200">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-200">Click **Publish**.</span></span> <span data-ttu-id="6f76b-201">Visual Studio публикует приложение в Azure.</span><span class="sxs-lookup"><span data-stu-id="6f76b-201">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="6f76b-202">По завершении развертывания приложение открывается в браузере.</span><span class="sxs-lookup"><span data-stu-id="6f76b-202">When the deployment completes, the app is opened in a browser.</span></span>

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="6f76b-204">Обновление приложения</span><span class="sxs-lookup"><span data-stu-id="6f76b-204">Update the app</span></span>

* <span data-ttu-id="6f76b-205">Измените страницу Razor *Pages/Index.cshtml* и ее содержимое.</span><span class="sxs-lookup"><span data-stu-id="6f76b-205">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="6f76b-206">Например, вы можете изменить абзац на "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="6f76b-206">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="6f76b-207">На странице **Сводка** для профиля публикации снова нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-207">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Страница со сводными данными о профиле публикации](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="6f76b-209">После публикации приложения убедитесь, что внесенные изменения доступны в Azure.</span><span class="sxs-lookup"><span data-stu-id="6f76b-209">After the app is published, verify the changes you made are available on Azure.</span></span>

![Убедитесь, что задача завершена.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="6f76b-211">Очистка</span><span class="sxs-lookup"><span data-stu-id="6f76b-211">Clean up</span></span>

<span data-ttu-id="6f76b-212">Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.</span><span class="sxs-lookup"><span data-stu-id="6f76b-212">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="6f76b-213">Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="6f76b-213">Select **Resource groups**, then select the resource group you created.</span></span>

![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="6f76b-215">На странице **Группы ресурсов** выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-215">In the **Resource groups** page, select **Delete**.</span></span>

![Портал Azure: страница "Группы ресурсов"](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="6f76b-217">Введите имя группы ресурсов и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-217">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="6f76b-218">Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.</span><span class="sxs-lookup"><span data-stu-id="6f76b-218">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="6f76b-219">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="6f76b-219">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="6f76b-220">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6f76b-220">Additional resources</span></span>

* <span data-ttu-id="6f76b-221">Сведения о Visual Studio Code см. в разделе [Профили публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="6f76b-221">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* <span data-ttu-id="6f76b-222">[служба приложений Azure](/azure/app-service/app-service-web-overview);</span><span class="sxs-lookup"><span data-stu-id="6f76b-222">[Azure App Service](/azure/app-service/app-service-web-overview)</span></span>
* [<span data-ttu-id="6f76b-223">Группа ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="6f76b-223">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="6f76b-224">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="6f76b-224">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
