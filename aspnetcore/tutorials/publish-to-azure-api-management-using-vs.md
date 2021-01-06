---
title: Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio
author: codemillmatt
description: Узнайте, как опубликовать веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 11/22/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 395b5981a3018486235c38f032893f985ab71383
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "96332239"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="437dd-103">Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="437dd-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="437dd-104">Автор: [Мэтт Сукуп](https://twitter.com/codemillmatt) (Matt Soucoup)</span><span class="sxs-lookup"><span data-stu-id="437dd-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="437dd-105">Настройка</span><span class="sxs-lookup"><span data-stu-id="437dd-105">Set up</span></span>

- <span data-ttu-id="437dd-106">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="437dd-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="437dd-107">[Создайте новый экземпляр службы управления API Azure](/azure/api-management/get-started-create-service-instance), если вы еще этого не сделали.</span><span class="sxs-lookup"><span data-stu-id="437dd-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="437dd-108">[Создайте проект приложения веб-API](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="437dd-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="437dd-109">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="437dd-109">Configure the app</span></span>

<span data-ttu-id="437dd-110">Добавление определений Swagger в веб-API ASP.NET Core позволяет службе управления API Azure считывать определения API приложения.</span><span class="sxs-lookup"><span data-stu-id="437dd-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="437dd-111">Выполните следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="437dd-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="437dd-112">Добавление Swagger</span><span class="sxs-lookup"><span data-stu-id="437dd-112">Add Swagger</span></span>

1. <span data-ttu-id="437dd-113">Добавьте пакет NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) в проект веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="437dd-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Снимок экрана для настройки диалогового окна NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="437dd-115">Добавьте следующую строку в метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="437dd-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="437dd-116">Добавьте следующую строку в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="437dd-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="437dd-117">Изменение маршрутизации API</span><span class="sxs-lookup"><span data-stu-id="437dd-117">Change the API routing</span></span>

<span data-ttu-id="437dd-118">Далее необходимо изменить структуру URL-адреса, требуемую для доступа к действию `Get` в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="437dd-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="437dd-119">Выполните следующие шаги:</span><span class="sxs-lookup"><span data-stu-id="437dd-119">Complete the following steps:</span></span>

1. <span data-ttu-id="437dd-120">Откройте файл *WeatherForecastController.cs*.</span><span class="sxs-lookup"><span data-stu-id="437dd-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="437dd-121">Удалите атрибут `[Route("[controller]")]` уровня класса.</span><span class="sxs-lookup"><span data-stu-id="437dd-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="437dd-122">Определение класса будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="437dd-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="437dd-123">Добавьте атрибут `[Route("/")]` к действию `Get()`.</span><span class="sxs-lookup"><span data-stu-id="437dd-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="437dd-124">Определение функции будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="437dd-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="437dd-125">Публикация веб-API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="437dd-126">Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="437dd-127">Публикация приложения API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="437dd-128">Добавьте пустой API в экземпляр службы управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="437dd-129">Опубликуйте приложение веб-API ASP.NET Core в экземпляре службы управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="437dd-130">Публикация приложения API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="437dd-131">Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="437dd-132">В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите действие **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="437dd-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="437dd-134">В диалоговом окне **Публикация** выберите **Azure** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="437dd-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Диалоговое окно публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="437dd-136">Выберите **Служба приложений Azure (Windows)** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="437dd-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Диалоговое окно публикации: выбор службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="437dd-138">Выберите **Создание новой службы приложений Azure**.</span><span class="sxs-lookup"><span data-stu-id="437dd-138">Select **Create a new Azure App Service**.</span></span>

    ![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="437dd-140">Откроется диалоговое окно **Создать службу приложений**.</span><span class="sxs-lookup"><span data-stu-id="437dd-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="437dd-141">Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="437dd-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="437dd-142">Вы можете сохранить эти имена или изменить их.</span><span class="sxs-lookup"><span data-stu-id="437dd-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="437dd-143">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="437dd-143">Select the **Create** button.</span></span>

    ![Диалоговое окно "Создание службы приложений"](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="437dd-145">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="437dd-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="437dd-146">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="437dd-146">The instance that was created is automatically selected.</span></span>

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="437dd-148">На этом этапе необходимо добавить API в службу управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="437dd-149">Не открывайте Visual Studio, пока выполняются следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="437dd-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="437dd-150">Добавление API в службу управления API Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="437dd-151">Откройте экземпляр службы управления API, созданный ранее на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="437dd-152">Выберите колонку **API-интерфейсов**.</span><span class="sxs-lookup"><span data-stu-id="437dd-152">Select the **APIs** blade:</span></span>

  ![Колонка API-интерфейсов, выбранная из экземпляра службы управления API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="437dd-154">Выберите три точки рядом с **API Echo** и выберите **Удалить** во всплывающем меню, чтобы удалить его.</span><span class="sxs-lookup"><span data-stu-id="437dd-154">Select the 3 dots next to **Echo API** and then select **Delete** from the pop-up menu to remove it.</span></span>

  ![Удаление API Echo из экземпляра службы Управления API](publish-to-azure-api-management-using-vs/_static/portal_delete_echo.png)

1. <span data-ttu-id="437dd-156">На панели **Добавить новый API** выберите плитку **Пустой API**.</span><span class="sxs-lookup"><span data-stu-id="437dd-156">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

  ![Экран, на котором выделена плитка пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="437dd-158">Введите следующие значения в диалоговом окне **Создание пустого API**, которое появляется.</span><span class="sxs-lookup"><span data-stu-id="437dd-158">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="437dd-159">**Отображаемое имя**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="437dd-159">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="437dd-160">**Имя**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="437dd-160">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="437dd-161">**Суффикс URL-адреса API**: *v1*</span><span class="sxs-lookup"><span data-stu-id="437dd-161">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="437dd-162">Оставьте поле **URL-адрес веб-службы** пустым.</span><span class="sxs-lookup"><span data-stu-id="437dd-162">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="437dd-163">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="437dd-163">Select the **Create** button.</span></span>

    ![Снимок экрана: диалоговое окно завершения создания пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="437dd-165">Создается пустой API.</span><span class="sxs-lookup"><span data-stu-id="437dd-165">The blank API is created.</span></span>

![Снимок экрана пустого API на портале управления API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="437dd-167">Публикация веб-API ASP.NET Core в службе управления API Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-167">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="437dd-168">Вернитесь в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="437dd-168">Switch back to Visual Studio.</span></span> <span data-ttu-id="437dd-169">Диалоговое окно **Публикация** должно оставаться открытым там, где вы остановились ранее.</span><span class="sxs-lookup"><span data-stu-id="437dd-169">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="437dd-170">Выберите только что опубликованную службу приложений Azure, чтобы выделить ее.</span><span class="sxs-lookup"><span data-stu-id="437dd-170">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="437dd-171">Нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="437dd-171">Select the **Next** button.</span></span>

    ![Снимок экрана: диалоговое окно публикации с выбранной службой приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="437dd-173">Теперь в диалоговом окне отображается служба управления API Azure, созданная ранее.</span><span class="sxs-lookup"><span data-stu-id="437dd-173">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="437dd-174">Разверните его и папку *интерфейсов API*, чтобы увидеть созданный пустой API.</span><span class="sxs-lookup"><span data-stu-id="437dd-174">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="437dd-175">Выберите имя пустого API и нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="437dd-175">Select the blank API's name and select the **Finish** button.</span></span>

    ![Снимок экрана с недавно созданным пустым API службы управления API Azure, выбранным в диалоговом окне публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="437dd-177">Диалоговое окно закроется, а затем появится экран сводки со сведениями о публикации.</span><span class="sxs-lookup"><span data-stu-id="437dd-177">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="437dd-178">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="437dd-178">Select the **Publish** button.</span></span>

    ![Снимок экрана Visual Studio с отображаемым профилем публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="437dd-180">Веб-API будет опубликован как в службе приложений Azure, так и в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-180">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="437dd-181">Появится новое окно браузера, в котором будет показан API, работающий в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-181">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="437dd-182">Это окно можно закрыть.</span><span class="sxs-lookup"><span data-stu-id="437dd-182">You can close that window.</span></span>

1. <span data-ttu-id="437dd-183">Перейдите к экземпляру управления API Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-183">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="437dd-184">Обновите приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="437dd-184">Refresh the browser window.</span></span>
1. <span data-ttu-id="437dd-185">Выберите пустой API, созданный на предыдущих шагах.</span><span class="sxs-lookup"><span data-stu-id="437dd-185">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="437dd-186">Теперь он заполнен, и вы можете изучить его.</span><span class="sxs-lookup"><span data-stu-id="437dd-186">It's now populated and you can explore around.</span></span>

    ![Снимок экрана с заполненным API в службе управления API Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="437dd-188">Настройка имени опубликованного API</span><span class="sxs-lookup"><span data-stu-id="437dd-188">Configure the published API name</span></span>

<span data-ttu-id="437dd-189">Обратите внимание, что имя API отличается от имени, которое вы присвоили.</span><span class="sxs-lookup"><span data-stu-id="437dd-189">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="437dd-190">Опубликованный API называется *WeatherAPI*; однако вы присваивали ему имя *WeatherForecasts* при его создании.</span><span class="sxs-lookup"><span data-stu-id="437dd-190">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="437dd-191">Чтобы переименовать его, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="437dd-191">Complete the following steps to fix the name:</span></span>

![Снимок экрана API с разными именами, которые выделены](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="437dd-193">Удалите следующую строку из метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="437dd-193">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="437dd-194">Добавьте следующий код в метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="437dd-194">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("WeatherForecasts", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "Weather Forecasts",
            Version = "v1"
        });
    });
    ```

1. <span data-ttu-id="437dd-195">Откройте созданный профиль публикации.</span><span class="sxs-lookup"><span data-stu-id="437dd-195">Open the newly created publish profile.</span></span> <span data-ttu-id="437dd-196">Его можно найти в **обозревателе решений** в папке *Properties/PublishProfiles*.</span><span class="sxs-lookup"><span data-stu-id="437dd-196">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Снимок экрана с выделенным расположением файла профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="437dd-198">Измените значение элемента `<OpenAPIDocumentName>` с `v1` на `WeatherForecasts`.</span><span class="sxs-lookup"><span data-stu-id="437dd-198">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![Снимок экрана изменений, необходимых для профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="437dd-200">Повторно опубликуйте веб-API ASP.NET Core и откройте экземпляр службы управления API Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-200">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="437dd-201">Обновите страницу в браузере.</span><span class="sxs-lookup"><span data-stu-id="437dd-201">Refresh the page in your browser.</span></span> <span data-ttu-id="437dd-202">Теперь отобразится правильное имя API.</span><span class="sxs-lookup"><span data-stu-id="437dd-202">You'll see the name of the API is now correct.</span></span>

    ![Снимок экрана готового API на портале](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="437dd-204">Проверка работоспособности веб-API</span><span class="sxs-lookup"><span data-stu-id="437dd-204">Verify the web API is working</span></span>

<span data-ttu-id="437dd-205">Вы можете протестировать развернутый веб-API ASP.NET Core в службе управления API Azure на портале Azure, выполнив следующие действия.</span><span class="sxs-lookup"><span data-stu-id="437dd-205">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="437dd-206">Откройте вкладку **Тест**.</span><span class="sxs-lookup"><span data-stu-id="437dd-206">Open the **Test** tab.</span></span>
1. <span data-ttu-id="437dd-207">Выберите **/** или операцию **Get**.</span><span class="sxs-lookup"><span data-stu-id="437dd-207">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="437dd-208">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="437dd-208">Select **Send**.</span></span>

    ![Снимок экрана портала перед тестом](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="437dd-210">Успешный ответ будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="437dd-210">A successful response will look like the following:</span></span>

![Снимок экрана успешного ответа от службы управления API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="437dd-212">Очистка</span><span class="sxs-lookup"><span data-stu-id="437dd-212">Clean up</span></span>

<span data-ttu-id="437dd-213">Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.</span><span class="sxs-lookup"><span data-stu-id="437dd-213">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="437dd-214">Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="437dd-214">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="437dd-216">На странице **Группы ресурсов** выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="437dd-216">In the **Resource groups** page, select **Delete**.</span></span>

    ![Портал Azure: страница "Группы ресурсов"](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="437dd-218">Введите имя группы ресурсов и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="437dd-218">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="437dd-219">Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.</span><span class="sxs-lookup"><span data-stu-id="437dd-219">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="437dd-220">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="437dd-220">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="437dd-221">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="437dd-221">Additional resources</span></span>

- [<span data-ttu-id="437dd-222">Управление API Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-222">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="437dd-223">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="437dd-223">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
