---
title: Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio
author: codemillmatt
description: Узнайте, как опубликовать веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio.
ms.author: masoucou
ms.custom: devx-track-csharp, mvc
ms.date: 08/26/2020
uid: tutorials/publish-to-azure-api-management-using-vs
ms.openlocfilehash: 3cc6b8c0bd93f133151e1c8ad18a55b11975a9be
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945516"
---
# <a name="publish-an-aspnet-core-web-api-to-azure-api-management-with-visual-studio"></a><span data-ttu-id="d4f94-103">Публикация веб-API ASP.NET Core в службе управления API Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4f94-103">Publish an ASP.NET Core web API to Azure API Management with Visual Studio</span></span>

<span data-ttu-id="d4f94-104">Автор: [Мэтт Сукуп](https://twitter.com/codemillmatt) (Matt Soucoup)</span><span class="sxs-lookup"><span data-stu-id="d4f94-104">By [Matt Soucoup](https://twitter.com/codemillmatt)</span></span>

## <a name="set-up"></a><span data-ttu-id="d4f94-105">Настройка</span><span class="sxs-lookup"><span data-stu-id="d4f94-105">Set up</span></span>

- <span data-ttu-id="d4f94-106">Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет.</span><span class="sxs-lookup"><span data-stu-id="d4f94-106">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="d4f94-107">[Создайте новый экземпляр службы управления API Azure](/azure/api-management/get-started-create-service-instance), если вы еще этого не сделали.</span><span class="sxs-lookup"><span data-stu-id="d4f94-107">[Create a new Azure API Management instance](/azure/api-management/get-started-create-service-instance) if you haven't already.</span></span>
- <span data-ttu-id="d4f94-108">[Создайте проект приложения веб-API](xref:tutorials/first-web-api#create-a-web-project).</span><span class="sxs-lookup"><span data-stu-id="d4f94-108">[Create a web API app project](xref:tutorials/first-web-api#create-a-web-project).</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="d4f94-109">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="d4f94-109">Configure the app</span></span>

<span data-ttu-id="d4f94-110">Добавление определений Swagger в веб-API ASP.NET Core позволяет службе управления API Azure считывать определения API приложения.</span><span class="sxs-lookup"><span data-stu-id="d4f94-110">Adding Swagger definitions to the ASP.NET Core web API allows Azure API Management to read the app's API definitions.</span></span> <span data-ttu-id="d4f94-111">Выполните следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="d4f94-111">Complete the following steps.</span></span>

### <a name="add-swagger"></a><span data-ttu-id="d4f94-112">Добавление Swagger</span><span class="sxs-lookup"><span data-stu-id="d4f94-112">Add Swagger</span></span>

1. <span data-ttu-id="d4f94-113">Добавьте пакет NuGet [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) в проект веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4f94-113">Add the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore) NuGet package to the ASP.NET Core web API project:</span></span>

    ![Снимок экрана для настройки диалогового окна NuGet](publish-to-azure-api-management-using-vs/_static/configure_nuget.png)

1. <span data-ttu-id="d4f94-115">Добавьте следующую строку в метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d4f94-115">Add the following line to the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```
    
1. <span data-ttu-id="d4f94-116">Добавьте следующую строку в метод `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d4f94-116">Add the following line to the `Startup.Configure` method:</span></span>

    ```csharp
    app.UseSwagger();
    ```

### <a name="change-the-api-routing"></a><span data-ttu-id="d4f94-117">Изменение маршрутизации API</span><span class="sxs-lookup"><span data-stu-id="d4f94-117">Change the API routing</span></span>

<span data-ttu-id="d4f94-118">Далее необходимо изменить структуру URL-адреса, требуемую для доступа к действию `Get` в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="d4f94-118">Next, you'll change the URL structure needed to access the `Get` action of the `WeatherForecastController`.</span></span> <span data-ttu-id="d4f94-119">Выполните следующие шаги:</span><span class="sxs-lookup"><span data-stu-id="d4f94-119">Complete the following steps:</span></span>

1. <span data-ttu-id="d4f94-120">Откройте файл *WeatherForecastController.cs*.</span><span class="sxs-lookup"><span data-stu-id="d4f94-120">Open the *WeatherForecastController.cs* file.</span></span>
1. <span data-ttu-id="d4f94-121">Удалите атрибут `[Route("[controller]")]` уровня класса.</span><span class="sxs-lookup"><span data-stu-id="d4f94-121">Delete the `[Route("[controller]")]` class-level attribute.</span></span> <span data-ttu-id="d4f94-122">Определение класса будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="d4f94-122">The class definition will look like the following:</span></span>

    ```csharp
    [ApiController]
    public class WeatherForecastController : ControllerBase
    ```

1. <span data-ttu-id="d4f94-123">Добавьте атрибут `[Route("/")]` к действию `Get()`.</span><span class="sxs-lookup"><span data-stu-id="d4f94-123">Add a `[Route("/")]` attribute to the `Get()` action.</span></span> <span data-ttu-id="d4f94-124">Определение функции будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="d4f94-124">The function definition will look like the following:</span></span>

    ```csharp
    [HttpGet]
    [Route("/")]
    public IEnumerable<WeatherForecast> Get()
    ```

## <a name="publish-the-web-api-to-azure-app-service"></a><span data-ttu-id="d4f94-125">Публикация веб-API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-125">Publish the web API to Azure App Service</span></span>

<span data-ttu-id="d4f94-126">Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-126">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="d4f94-127">Публикация приложения API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-127">Publish the API app to Azure App Service.</span></span>
1. <span data-ttu-id="d4f94-128">Добавьте пустой API в экземпляр службы управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-128">Add a blank API to the Azure API Management service instance.</span></span>
1. <span data-ttu-id="d4f94-129">Опубликуйте приложение веб-API ASP.NET Core в экземпляре службы управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-129">Publish the ASP.NET Core web API app to the Azure API Management service instance.</span></span>

### <a name="publish-the-api-app-to-azure-app-service"></a><span data-ttu-id="d4f94-130">Публикация приложения API в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-130">Publish the API app to Azure App Service</span></span>

<span data-ttu-id="d4f94-131">Выполните следующие действия, чтобы опубликовать веб-API ASP.NET Core в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-131">Complete the following steps to publish the ASP.NET Core web API to Azure API Management:</span></span>

1. <span data-ttu-id="d4f94-132">В **Обозревателе решений** щелкните проект правой кнопкой мыши и выберите действие **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-132">In **Solution Explorer**, right-click the project and select **Publish**:</span></span>

    ![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-api-management-using-vs/_static/publish_menu.png)

1. <span data-ttu-id="d4f94-134">В диалоговом окне **Публикация** выберите **Azure** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-134">In the **Publish** dialog, select **Azure** and select the **Next** button:</span></span>

    ![Диалоговое окно публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog.png)

1. <span data-ttu-id="d4f94-136">Выберите **Служба приложений Azure (Windows)** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-136">Select **Azure App Service (Windows)** and select the **Next** button:</span></span>

    ![Диалоговое окно публикации: выбор службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc.png)

1. <span data-ttu-id="d4f94-138">Выберите **Создание новой службы приложений Azure**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-138">Select **Create a new Azure App Service**.</span></span>

    ![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-api-management-using-vs/_static/publish_dialog_create_new_app_svc.png)

    <span data-ttu-id="d4f94-140">Откроется диалоговое окно **Создать службу приложений**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-140">The **Create App Service** dialog appears.</span></span> <span data-ttu-id="d4f94-141">Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-141">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="d4f94-142">Вы можете сохранить эти имена или изменить их.</span><span class="sxs-lookup"><span data-stu-id="d4f94-142">You can keep these names or change them.</span></span>
1. <span data-ttu-id="d4f94-143">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-143">Select the **Create** button.</span></span>

    ![Диалоговое окно "Создание службы приложений"](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_attributes.png)

<span data-ttu-id="d4f94-145">Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-145">After creation is completed, the dialog is automatically closed and the **Publish** dialog gets focus again.</span></span> <span data-ttu-id="d4f94-146">Созданный экземпляр выбирается автоматически.</span><span class="sxs-lookup"><span data-stu-id="d4f94-146">The instance that was created is automatically selected.</span></span>

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

<span data-ttu-id="d4f94-148">На этом этапе необходимо добавить API в службу управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-148">At this point, you need to add an API to the Azure API Management service.</span></span> <span data-ttu-id="d4f94-149">Не открывайте Visual Studio, пока выполняются следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="d4f94-149">Leave Visual Studio open while you complete the following tasks.</span></span>

### <a name="add-an-api-to-azure-api-management"></a><span data-ttu-id="d4f94-150">Добавление API в службу управления API Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-150">Add an API to Azure API Management</span></span>

1. <span data-ttu-id="d4f94-151">Откройте экземпляр службы управления API, созданный ранее на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-151">Open the API Management Service instance created previously in the Azure portal.</span></span> <span data-ttu-id="d4f94-152">Выберите колонку **API-интерфейсов**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-152">Select the **APIs** blade:</span></span>

    ![Колонка API-интерфейсов, выбранная из экземпляра службы управления API](publish-to-azure-api-management-using-vs/_static/portal_api_overview.png)

1. <span data-ttu-id="d4f94-154">На панели **Добавить новый API** выберите плитку **Пустой API**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-154">From the **Add a new API** panel, select the **Blank API** tile:</span></span>

    ![Экран, на котором выделена плитка пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_create_blank.png)

1. <span data-ttu-id="d4f94-156">Введите следующие значения в диалоговом окне **Создание пустого API**, которое появляется.</span><span class="sxs-lookup"><span data-stu-id="d4f94-156">Enter the following values in the **Create a blank API** dialog that appears:</span></span>    

    - <span data-ttu-id="d4f94-157">**Отображаемое имя**: *WeatherForecasts*</span><span class="sxs-lookup"><span data-stu-id="d4f94-157">**Display Name**: *WeatherForecasts*</span></span>
    - <span data-ttu-id="d4f94-158">**Имя**: *weatherforecasts*</span><span class="sxs-lookup"><span data-stu-id="d4f94-158">**Name**: *weatherforecasts*</span></span>
    - <span data-ttu-id="d4f94-159">**Суффикс URL-адреса API**: *v1*</span><span class="sxs-lookup"><span data-stu-id="d4f94-159">**API Url suffix**: *v1*</span></span>
    - <span data-ttu-id="d4f94-160">Оставьте поле **URL-адрес веб-службы** пустым.</span><span class="sxs-lookup"><span data-stu-id="d4f94-160">Leave the **Web service URL** field empty.</span></span>

1. <span data-ttu-id="d4f94-161">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-161">Select the **Create** button.</span></span>

    ![Снимок экрана: диалоговое окно завершения создания пустого API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_complete.png)

<span data-ttu-id="d4f94-163">Создается пустой API.</span><span class="sxs-lookup"><span data-stu-id="d4f94-163">The blank API is created.</span></span>

![Снимок экрана пустого API на портале управления API](publish-to-azure-api-management-using-vs/_static/portal_api_blank_created_empty.png)

### <a name="publish-the-aspnet-core-web-api-to-azure-api-management"></a><span data-ttu-id="d4f94-165">Публикация веб-API ASP.NET Core в службе управления API Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-165">Publish the ASP.NET Core web API to Azure API Management</span></span>

1. <span data-ttu-id="d4f94-166">Вернитесь в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d4f94-166">Switch back to Visual Studio.</span></span> <span data-ttu-id="d4f94-167">Диалоговое окно **Публикация** должно оставаться открытым там, где вы остановились ранее.</span><span class="sxs-lookup"><span data-stu-id="d4f94-167">The **Publish** dialog should still be open where you left off before.</span></span>
1. <span data-ttu-id="d4f94-168">Выберите только что опубликованную службу приложений Azure, чтобы выделить ее.</span><span class="sxs-lookup"><span data-stu-id="d4f94-168">Select the newly published Azure App Service so it's highlighted.</span></span>
1. <span data-ttu-id="d4f94-169">Нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-169">Select the **Next** button.</span></span>

    ![Снимок экрана: диалоговое окно публикации с выбранной службой приложений](publish-to-azure-api-management-using-vs/_static/publish_dialog_app_svc_created.png)

1. <span data-ttu-id="d4f94-171">Теперь в диалоговом окне отображается служба управления API Azure, созданная ранее.</span><span class="sxs-lookup"><span data-stu-id="d4f94-171">The dialog now shows the Azure API Management service created before.</span></span> <span data-ttu-id="d4f94-172">Разверните его и папку *интерфейсов API*, чтобы увидеть созданный пустой API.</span><span class="sxs-lookup"><span data-stu-id="d4f94-172">Expand it and the *APIs* folder to see the blank API you created.</span></span>
1. <span data-ttu-id="d4f94-173">Выберите имя пустого API и нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-173">Select the blank API's name and select the **Finish** button.</span></span>

    ![Снимок экрана с недавно созданным пустым API службы управления API Azure, выбранным в диалоговом окне публикации](publish-to-azure-api-management-using-vs/_static/publish_dialog_api_selected.png)

1. <span data-ttu-id="d4f94-175">Диалоговое окно закроется, а затем появится экран сводки со сведениями о публикации.</span><span class="sxs-lookup"><span data-stu-id="d4f94-175">The dialog closes and a summary screen appears with information about the publish.</span></span> <span data-ttu-id="d4f94-176">Нажмите кнопку **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-176">Select the **Publish** button.</span></span>

    ![Снимок экрана Visual Studio с отображаемым профилем публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile.png)

    <span data-ttu-id="d4f94-178">Веб-API будет опубликован как в службе приложений Azure, так и в службе управления API Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-178">The web API will publish to both Azure App Service and Azure API Management.</span></span> <span data-ttu-id="d4f94-179">Появится новое окно браузера, в котором будет показан API, работающий в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-179">A new browser window will appear and show the API running in Azure App Service.</span></span> <span data-ttu-id="d4f94-180">Это окно можно закрыть.</span><span class="sxs-lookup"><span data-stu-id="d4f94-180">You can close that window.</span></span>

1. <span data-ttu-id="d4f94-181">Перейдите к экземпляру управления API Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-181">Switch back to the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="d4f94-182">Обновите приложение в браузере.</span><span class="sxs-lookup"><span data-stu-id="d4f94-182">Refresh the browser window.</span></span>
1. <span data-ttu-id="d4f94-183">Выберите пустой API, созданный на предыдущих шагах.</span><span class="sxs-lookup"><span data-stu-id="d4f94-183">Select the blank API you created in the preceding steps.</span></span> <span data-ttu-id="d4f94-184">Теперь он заполнен, и вы можете изучить его.</span><span class="sxs-lookup"><span data-stu-id="d4f94-184">It's now populated and you can explore around.</span></span>

    ![Снимок экрана с заполненным API в службе управления API Azure](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt.png)

### <a name="configure-the-published-api-name"></a><span data-ttu-id="d4f94-186">Настройка имени опубликованного API</span><span class="sxs-lookup"><span data-stu-id="d4f94-186">Configure the published API name</span></span>

<span data-ttu-id="d4f94-187">Обратите внимание, что имя API отличается от имени, которое вы присвоили.</span><span class="sxs-lookup"><span data-stu-id="d4f94-187">Notice the name of the API is different than what you named it.</span></span> <span data-ttu-id="d4f94-188">Опубликованный API называется *WeatherAPI*; однако вы присваивали ему имя *WeatherForecasts* при его создании.</span><span class="sxs-lookup"><span data-stu-id="d4f94-188">The published API is named *WeatherAPI*; however, you named it *WeatherForecasts* when you created it.</span></span> <span data-ttu-id="d4f94-189">Чтобы переименовать его, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d4f94-189">Complete the following steps to fix the name:</span></span>

![Снимок экрана API с разными именами, которые выделены](publish-to-azure-api-management-using-vs/_static/deployed_to_azure_api_mgmt_wrong_name.png)

1. <span data-ttu-id="d4f94-191">Удалите следующую строку из метода `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d4f94-191">Delete the following line from the `Startup.ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddSwaggerGen();
    ```

1. <span data-ttu-id="d4f94-192">Добавьте следующий код в метод `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d4f94-192">Add the following code to the `Startup.ConfigureServices` method:</span></span>
    
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

1. <span data-ttu-id="d4f94-193">Откройте созданный профиль публикации.</span><span class="sxs-lookup"><span data-stu-id="d4f94-193">Open the newly created publish profile.</span></span> <span data-ttu-id="d4f94-194">Его можно найти в **обозревателе решений** в папке *Properties/PublishProfiles*.</span><span class="sxs-lookup"><span data-stu-id="d4f94-194">It can be found from **Solution Explorer** in the *Properties/PublishProfiles* folder.</span></span>

    ![Снимок экрана с выделенным расположением файла профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_highlighted.png)

1. <span data-ttu-id="d4f94-196">Измените значение элемента `<OpenAPIDocumentName>` с `v1` на `WeatherForecasts`.</span><span class="sxs-lookup"><span data-stu-id="d4f94-196">Change the `<OpenAPIDocumentName>` element's value from `v1` to `WeatherForecasts`.</span></span>

    ![Снимок экрана изменений, необходимых для профиля публикации](publish-to-azure-api-management-using-vs/_static/vs_publish_profile_changes.png)

1. <span data-ttu-id="d4f94-198">Повторно опубликуйте веб-API ASP.NET Core и откройте экземпляр службы управления API Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-198">Republish the ASP.NET Core web API and open the Azure API Management instance in the Azure portal.</span></span>
1. <span data-ttu-id="d4f94-199">Обновите страницу в браузере.</span><span class="sxs-lookup"><span data-stu-id="d4f94-199">Refresh the page in your browser.</span></span> <span data-ttu-id="d4f94-200">Теперь отобразится правильное имя API.</span><span class="sxs-lookup"><span data-stu-id="d4f94-200">You'll see the name of the API is now correct.</span></span>

    ![Снимок экрана готового API на портале](publish-to-azure-api-management-using-vs/_static/portal_finish.png)

### <a name="verify-the-web-api-is-working"></a><span data-ttu-id="d4f94-202">Проверка работоспособности веб-API</span><span class="sxs-lookup"><span data-stu-id="d4f94-202">Verify the web API is working</span></span>

<span data-ttu-id="d4f94-203">Вы можете протестировать развернутый веб-API ASP.NET Core в службе управления API Azure на портале Azure, выполнив следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d4f94-203">You can test the deployed ASP.NET Core web API in Azure API Management from the Azure portal with the following steps:</span></span>

1. <span data-ttu-id="d4f94-204">Откройте вкладку **Тест**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-204">Open the **Test** tab.</span></span>
1. <span data-ttu-id="d4f94-205">Выберите **/** или операцию **Get**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-205">Select **/** or the **Get** operation.</span></span>
1. <span data-ttu-id="d4f94-206">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-206">Select **Send**.</span></span>

    ![Снимок экрана портала перед тестом](publish-to-azure-api-management-using-vs/_static/portal_pre_test.png)

<span data-ttu-id="d4f94-208">Успешный ответ будет выглядеть следующим образом.</span><span class="sxs-lookup"><span data-stu-id="d4f94-208">A successful response will look like the following:</span></span>

![Снимок экрана успешного ответа от службы управления API](publish-to-azure-api-management-using-vs/_static/portal_successful_response.png)

## <a name="clean-up"></a><span data-ttu-id="d4f94-210">Очистка</span><span class="sxs-lookup"><span data-stu-id="d4f94-210">Clean up</span></span>

<span data-ttu-id="d4f94-211">Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.</span><span class="sxs-lookup"><span data-stu-id="d4f94-211">When you've finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

1. <span data-ttu-id="d4f94-212">Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.</span><span class="sxs-lookup"><span data-stu-id="d4f94-212">Select **Resource groups**, then select the resource group you created.</span></span>

    ![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-api-management-using-vs/_static/portalrg.png)

1. <span data-ttu-id="d4f94-214">На странице **Группы ресурсов** выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-214">In the **Resource groups** page, select **Delete**.</span></span>

    ![Портал Azure: страница "Группы ресурсов"](publish-to-azure-api-management-using-vs/_static/rgd.png)

1. <span data-ttu-id="d4f94-216">Введите имя группы ресурсов и выберите **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="d4f94-216">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="d4f94-217">Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.</span><span class="sxs-lookup"><span data-stu-id="d4f94-217">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d4f94-218">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="d4f94-218">Next steps</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="d4f94-219">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d4f94-219">Additional resources</span></span>

- [<span data-ttu-id="d4f94-220">Управление API Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-220">Azure API Management</span></span>](/azure/api-management/api-management-key-concepts)
- [<span data-ttu-id="d4f94-221">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="d4f94-221">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
