---
<span data-ttu-id="34693-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="34693-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34693-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34693-102">'Blazor'</span></span>
- <span data-ttu-id="34693-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34693-103">'Identity'</span></span>
- <span data-ttu-id="34693-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34693-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="34693-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34693-105">'Razor'</span></span>
- <span data-ttu-id="34693-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="34693-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="34693-107">Поддержка служб IIS во время разработки в Visual Studio для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34693-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="34693-108">Автор [Сурабх Ширхатти](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="34693-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34693-109">В этой статье описаны поддерживаемые в [Visual Studio](https://visualstudio.microsoft.com) возможности для отладки приложений ASP.NET Core, выполняющихся в службах IIS в Windows Server.</span><span class="sxs-lookup"><span data-stu-id="34693-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="34693-110">Также здесь приведены пошаговые инструкции по включению этого сценария и настройке проекта.</span><span class="sxs-lookup"><span data-stu-id="34693-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34693-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="34693-111">Prerequisites</span></span>

* [<span data-ttu-id="34693-112">Visual Studio для Windows</span><span class="sxs-lookup"><span data-stu-id="34693-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="34693-113">Рабочая нагрузка **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="34693-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="34693-114">Рабочая нагрузка **Кроссплатформенная разработка .NET Core**</span><span class="sxs-lookup"><span data-stu-id="34693-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="34693-115">Сертификат безопасности X.509 (для поддержки HTTPS)</span><span class="sxs-lookup"><span data-stu-id="34693-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="34693-116">Активация IIS</span><span class="sxs-lookup"><span data-stu-id="34693-116">Enable IIS</span></span>

1. <span data-ttu-id="34693-117">В Windows последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="34693-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="34693-118">Установите флажок **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="34693-119">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="34693-119">Select **OK**.</span></span>

<span data-ttu-id="34693-120">Для установки служб IIS, возможно, потребуется перезагрузить компьютер.</span><span class="sxs-lookup"><span data-stu-id="34693-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="34693-121">Настройка IIS</span><span class="sxs-lookup"><span data-stu-id="34693-121">Configure IIS</span></span>

<span data-ttu-id="34693-122">В службах IIS нужно настроить веб-сайт со следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="34693-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="34693-123">**Имя узла.** Обычно используется **Веб-сайт по умолчанию** со значением `localhost`, заданным параметру **Имя узла**.</span><span class="sxs-lookup"><span data-stu-id="34693-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="34693-124">Но можно также задать любой допустимый веб-сайт IIS с уникальным именем узла.</span><span class="sxs-lookup"><span data-stu-id="34693-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="34693-125">**Привязка сайта**</span><span class="sxs-lookup"><span data-stu-id="34693-125">**Site Binding**</span></span>
  * <span data-ttu-id="34693-126">Для приложений, которым требуется протокол HTTPS, создайте привязку к порту 443 с помощью сертификата.</span><span class="sxs-lookup"><span data-stu-id="34693-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="34693-127">Обычно используется **сертификат разработки IIS Express**, но подойдет и любой другой действительный сертификат.</span><span class="sxs-lookup"><span data-stu-id="34693-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="34693-128">Для приложений, использующих протокол HTTP, подтвердите привязку к порту 80 или создайте привязку к порту 80 для нового сайта.</span><span class="sxs-lookup"><span data-stu-id="34693-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="34693-129">Используйте одну привязку либо для HTTP, либо для HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34693-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="34693-130">**Одновременная привязка к портам HTTP и HTTPS не поддерживается.**</span><span class="sxs-lookup"><span data-stu-id="34693-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="34693-131">Включение поддержки служб IIS в Visual Studio во время разработки</span><span class="sxs-lookup"><span data-stu-id="34693-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="34693-132">Запустите установщик Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34693-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="34693-133">Выберите **Изменить** в установщике программы Visual Studio, которую планируется использовать для поддержки IIS во время разработки.</span><span class="sxs-lookup"><span data-stu-id="34693-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="34693-134">Для рабочей нагрузки **ASP.NET и разработка веб-приложений** найдите и установите компонент **Поддержка времени разработки в IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="34693-135">Компонент находится в разделе **Дополнительно** под пунктом **Поддержка времени разработки в IIS** на панели **Сведения об установке** справа от рабочих нагрузок.</span><span class="sxs-lookup"><span data-stu-id="34693-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="34693-136">Этот компонент выполнит установку [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module), который является собственным модулем IIS, необходимым для запуска приложений ASP.NET Core со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="34693-137">Настройка проекта</span><span class="sxs-lookup"><span data-stu-id="34693-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="34693-138">Перенаправление HTTPS</span><span class="sxs-lookup"><span data-stu-id="34693-138">HTTPS redirection</span></span>

<span data-ttu-id="34693-139">Если новому проекту требуется протокол HTTPS, установите флажок **Configure for HTTPS** (Настроить для HTTPS) в окне **Создать веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="34693-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="34693-140">Установка флажка добавляет [ПО промежуточного слоя перенаправления HTTPS и HSTS](xref:security/enforcing-ssl) в приложение при его создании.</span><span class="sxs-lookup"><span data-stu-id="34693-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="34693-141">Если существующему проекту требуется протокол HTTPS, используйте ПО промежуточного слоя перенаправления HTTPS и HSTS в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34693-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="34693-142">Для получения дополнительной информации см. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="34693-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="34693-143">Для проекта, который использует протокол HTTP, поддержку [ПО промежуточного слоя перенаправления HTTPS и HSTS](xref:security/enforcing-ssl) задавать не нужно.</span><span class="sxs-lookup"><span data-stu-id="34693-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="34693-144">Настройка приложения не требуется.</span><span class="sxs-lookup"><span data-stu-id="34693-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="34693-145">Профиль запуска служб IIS</span><span class="sxs-lookup"><span data-stu-id="34693-145">IIS launch profile</span></span>

<span data-ttu-id="34693-146">Создайте новый профиль запуска, чтобы добавить поддержку IIS во время разработки.</span><span class="sxs-lookup"><span data-stu-id="34693-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="34693-147">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="34693-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="34693-148">Выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="34693-148">Select **Properties**.</span></span> <span data-ttu-id="34693-149">Откройте вкладку **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="34693-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="34693-150">В разделе **Профиль** нажмите кнопку **Новый**.</span><span class="sxs-lookup"><span data-stu-id="34693-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="34693-151">Во всплывающем окне присвойте новому профилю имя IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="34693-152">Нажмите кнопку **ОК**, чтобы создать проект.</span><span class="sxs-lookup"><span data-stu-id="34693-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="34693-153">В поле **Запуск** выберите из списка значение **IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="34693-154">Установите флажок **Запуск браузера** и укажите URL-адрес конечной точки.</span><span class="sxs-lookup"><span data-stu-id="34693-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="34693-155">Если приложению требуется протокол HTTPS, используйте конечную точку HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="34693-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="34693-156">Для протокола HTTP используйте конечную точку HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="34693-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="34693-157">Укажите то же имя узла и тот же порт, как в [выполненной ранее настройке IIS](#configure-iis). Обычно это `localhost`.</span><span class="sxs-lookup"><span data-stu-id="34693-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="34693-158">Укажите имя приложения в конце URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="34693-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="34693-159">Например `https://localhost/WebApplication1` (HTTPS) или `http://localhost/WebApplication1` (HTTP) являются действительными URL-адресами конечной точки.</span><span class="sxs-lookup"><span data-stu-id="34693-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="34693-160">В разделе **Переменные среды** нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="34693-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="34693-161">Для переменной среды задайте **имя**`ASPNETCORE_ENVIRONMENT` и **значение**`Development`.</span><span class="sxs-lookup"><span data-stu-id="34693-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="34693-162">В области **Параметры веб-сервера** в поле **URL-адрес приложения** задайте значение, соответствующее URL-адресу конечной точки в поле **Запуск браузера**.</span><span class="sxs-lookup"><span data-stu-id="34693-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="34693-163">В Visual Studio 2019 и последующих версиях параметру **Модель размещения** задайте значение **По умолчанию**, чтобы использовать модель размещения проекта.</span><span class="sxs-lookup"><span data-stu-id="34693-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="34693-164">Если для проекта задано свойство `<AspNetCoreHostingModel>` в файле проекта, используется значение свойства `InProcess` или `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="34693-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="34693-165">Если свойство не задано, используется модель размещения приложения по умолчанию In Process.</span><span class="sxs-lookup"><span data-stu-id="34693-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="34693-166">Если приложению требуется явно указать модель размещения, отличную от обычной модели размещения приложения, задайте параметру **Модель размещения** значение `In Process` или `Out Of Process` по необходимости.</span><span class="sxs-lookup"><span data-stu-id="34693-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="34693-167">Сохраните профиль.</span><span class="sxs-lookup"><span data-stu-id="34693-167">Save the profile.</span></span>

<span data-ttu-id="34693-168">Если Visual Studio не используется, можно вручную добавить профиль запуска в файл [launchSettings.json](https://json.schemastore.org/launchsettings) в папке *Properties*.</span><span class="sxs-lookup"><span data-stu-id="34693-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="34693-169">В следующем примере настраивается профиль для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34693-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="34693-170">Убедитесь, что конечные точки `applicationUrl` и `launchUrl` совпадают и используют тот же протокол (HTTP или HTTPS), что и конфигурация привязки IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="34693-171">Запуск проекта</span><span class="sxs-lookup"><span data-stu-id="34693-171">Run the project</span></span>

<span data-ttu-id="34693-172">Запустите Visual Studio от имени администратора.</span><span class="sxs-lookup"><span data-stu-id="34693-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="34693-173">Убедитесь, что для раскрывающегося списка с конфигурацией сборки построения выбрано значение **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="34693-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="34693-174">Настройте кнопку [Начать отладку](/visualstudio/debugger/debugger-feature-tour) на профиль **IIS** и нажмите ее для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="34693-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="34693-175">Если вы вошли в Visual Studio без прав администратора, возможно, потребуется перезапуск.</span><span class="sxs-lookup"><span data-stu-id="34693-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="34693-176">Перезапустите Visual Studio при появлении соответствующего запроса.</span><span class="sxs-lookup"><span data-stu-id="34693-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="34693-177">Если используется сертификат разработки без доверия, возможно, потребуется создать исключение для этого ненадежного сертификата по запросу в браузере.</span><span class="sxs-lookup"><span data-stu-id="34693-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="34693-178">Отладка конфигурации сборки выпуска с использованием функции [Только мой код](/visualstudio/debugger/just-my-code) и оптимизации компилятора приводит к ограничению возможностей.</span><span class="sxs-lookup"><span data-stu-id="34693-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="34693-179">Например, точки останова не будут достигнуты.</span><span class="sxs-lookup"><span data-stu-id="34693-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34693-180">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="34693-180">Additional resources</span></span>

* [<span data-ttu-id="34693-181">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="34693-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34693-182">В этой статье описаны поддерживаемые в [Visual Studio](https://visualstudio.microsoft.com) возможности для отладки приложений ASP.NET Core, выполняющихся в службах IIS в Windows Server.</span><span class="sxs-lookup"><span data-stu-id="34693-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="34693-183">Также здесь приведены пошаговые инструкции по включению этого сценария и настройке проекта.</span><span class="sxs-lookup"><span data-stu-id="34693-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34693-184">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="34693-184">Prerequisites</span></span>

* [<span data-ttu-id="34693-185">Visual Studio для Windows</span><span class="sxs-lookup"><span data-stu-id="34693-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="34693-186">Рабочая нагрузка **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="34693-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="34693-187">Рабочая нагрузка **Кроссплатформенная разработка .NET Core**</span><span class="sxs-lookup"><span data-stu-id="34693-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="34693-188">Сертификат безопасности X.509 (для поддержки HTTPS)</span><span class="sxs-lookup"><span data-stu-id="34693-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="34693-189">Активация IIS</span><span class="sxs-lookup"><span data-stu-id="34693-189">Enable IIS</span></span>

1. <span data-ttu-id="34693-190">В Windows последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="34693-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="34693-191">Установите флажок **Службы IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="34693-192">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="34693-192">Select **OK**.</span></span>

<span data-ttu-id="34693-193">Для установки служб IIS, возможно, потребуется перезагрузить компьютер.</span><span class="sxs-lookup"><span data-stu-id="34693-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="34693-194">Настройка IIS</span><span class="sxs-lookup"><span data-stu-id="34693-194">Configure IIS</span></span>

<span data-ttu-id="34693-195">В службах IIS нужно настроить веб-сайт со следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="34693-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="34693-196">**Имя узла.** Обычно используется **Веб-сайт по умолчанию** со значением `localhost`, заданным параметру **Имя узла**.</span><span class="sxs-lookup"><span data-stu-id="34693-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="34693-197">Но можно также задать любой допустимый веб-сайт IIS с уникальным именем узла.</span><span class="sxs-lookup"><span data-stu-id="34693-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="34693-198">**Привязка сайта**</span><span class="sxs-lookup"><span data-stu-id="34693-198">**Site Binding**</span></span>
  * <span data-ttu-id="34693-199">Для приложений, которым требуется протокол HTTPS, создайте привязку к порту 443 с помощью сертификата.</span><span class="sxs-lookup"><span data-stu-id="34693-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="34693-200">Обычно используется **сертификат разработки IIS Express**, но подойдет и любой другой действительный сертификат.</span><span class="sxs-lookup"><span data-stu-id="34693-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="34693-201">Для приложений, использующих протокол HTTP, подтвердите привязку к порту 80 или создайте привязку к порту 80 для нового сайта.</span><span class="sxs-lookup"><span data-stu-id="34693-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="34693-202">Используйте одну привязку либо для HTTP, либо для HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34693-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="34693-203">**Одновременная привязка к портам HTTP и HTTPS не поддерживается.**</span><span class="sxs-lookup"><span data-stu-id="34693-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="34693-204">Включение поддержки служб IIS в Visual Studio во время разработки</span><span class="sxs-lookup"><span data-stu-id="34693-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="34693-205">Запустите установщик Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34693-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="34693-206">Выберите **Изменить** в установщике программы Visual Studio, которую планируется использовать для поддержки IIS во время разработки.</span><span class="sxs-lookup"><span data-stu-id="34693-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="34693-207">Для рабочей нагрузки **ASP.NET и разработка веб-приложений** найдите и установите компонент **Поддержка времени разработки в IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="34693-208">Компонент находится в разделе **Дополнительно** под пунктом **Поддержка времени разработки в IIS** на панели **Сведения об установке** справа от рабочих нагрузок.</span><span class="sxs-lookup"><span data-stu-id="34693-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="34693-209">Этот компонент выполнит установку [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module), который является собственным модулем IIS, необходимым для запуска приложений ASP.NET Core со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="34693-210">Настройка проекта</span><span class="sxs-lookup"><span data-stu-id="34693-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="34693-211">Перенаправление HTTPS</span><span class="sxs-lookup"><span data-stu-id="34693-211">HTTPS redirection</span></span>

<span data-ttu-id="34693-212">Если новому проекту требуется протокол HTTPS, установите флажок **Configure for HTTPS** (Настроить для HTTPS) в окне **Создать веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="34693-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="34693-213">Установка флажка добавляет [ПО промежуточного слоя перенаправления HTTPS и HSTS](xref:security/enforcing-ssl) в приложение при его создании.</span><span class="sxs-lookup"><span data-stu-id="34693-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="34693-214">Если существующему проекту требуется протокол HTTPS, используйте ПО промежуточного слоя перенаправления HTTPS и HSTS в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34693-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="34693-215">Для получения дополнительной информации см. <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="34693-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="34693-216">Для проекта, который использует протокол HTTP, поддержку [ПО промежуточного слоя перенаправления HTTPS и HSTS](xref:security/enforcing-ssl) задавать не нужно.</span><span class="sxs-lookup"><span data-stu-id="34693-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="34693-217">Настройка приложения не требуется.</span><span class="sxs-lookup"><span data-stu-id="34693-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="34693-218">Профиль запуска служб IIS</span><span class="sxs-lookup"><span data-stu-id="34693-218">IIS launch profile</span></span>

<span data-ttu-id="34693-219">Создайте новый профиль запуска, чтобы добавить поддержку IIS во время разработки.</span><span class="sxs-lookup"><span data-stu-id="34693-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="34693-220">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="34693-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="34693-221">Выберите пункт **Свойства**.</span><span class="sxs-lookup"><span data-stu-id="34693-221">Select **Properties**.</span></span> <span data-ttu-id="34693-222">Откройте вкладку **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="34693-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="34693-223">В разделе **Профиль** нажмите кнопку **Новый**.</span><span class="sxs-lookup"><span data-stu-id="34693-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="34693-224">Во всплывающем окне присвойте новому профилю имя IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="34693-225">Нажмите кнопку **ОК**, чтобы создать проект.</span><span class="sxs-lookup"><span data-stu-id="34693-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="34693-226">В поле **Запуск** выберите из списка значение **IIS**.</span><span class="sxs-lookup"><span data-stu-id="34693-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="34693-227">Установите флажок **Запуск браузера** и укажите URL-адрес конечной точки.</span><span class="sxs-lookup"><span data-stu-id="34693-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="34693-228">Если приложению требуется протокол HTTPS, используйте конечную точку HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="34693-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="34693-229">Для протокола HTTP используйте конечную точку HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="34693-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="34693-230">Укажите то же имя узла и тот же порт, как в [выполненной ранее настройке IIS](#configure-iis). Обычно это `localhost`.</span><span class="sxs-lookup"><span data-stu-id="34693-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="34693-231">Укажите имя приложения в конце URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="34693-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="34693-232">Например `https://localhost/WebApplication1` (HTTPS) или `http://localhost/WebApplication1` (HTTP) являются действительными URL-адресами конечной точки.</span><span class="sxs-lookup"><span data-stu-id="34693-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="34693-233">В разделе **Переменные среды** нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="34693-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="34693-234">Для переменной среды задайте **имя**`ASPNETCORE_ENVIRONMENT` и **значение**`Development`.</span><span class="sxs-lookup"><span data-stu-id="34693-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="34693-235">В области **Параметры веб-сервера** в поле **URL-адрес приложения** задайте значение, соответствующее URL-адресу конечной точки в поле **Запуск браузера**.</span><span class="sxs-lookup"><span data-stu-id="34693-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="34693-236">В Visual Studio 2019 и последующих версиях параметру **Модель размещения** задайте значение **По умолчанию**, чтобы использовать модель размещения проекта.</span><span class="sxs-lookup"><span data-stu-id="34693-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="34693-237">Если для проекта задано свойство `<AspNetCoreHostingModel>` в файле проекта, используется значение свойства `InProcess` или `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="34693-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="34693-238">Если свойство не задано, используется модель размещения приложения по умолчанию Out Of Process.</span><span class="sxs-lookup"><span data-stu-id="34693-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="34693-239">Если приложению требуется явно указать модель размещения, отличную от обычной модели размещения приложения, задайте параметру **Модель размещения** значение `In Process` или `Out Of Process` по необходимости.</span><span class="sxs-lookup"><span data-stu-id="34693-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="34693-240">Сохраните профиль.</span><span class="sxs-lookup"><span data-stu-id="34693-240">Save the profile.</span></span>

<span data-ttu-id="34693-241">Если Visual Studio не используется, можно вручную добавить профиль запуска в файл [launchSettings.json](https://json.schemastore.org/launchsettings) в папке *Properties*.</span><span class="sxs-lookup"><span data-stu-id="34693-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="34693-242">В следующем примере настраивается профиль для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34693-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="34693-243">Убедитесь, что конечные точки `applicationUrl` и `launchUrl` совпадают и используют тот же протокол (HTTP или HTTPS), что и конфигурация привязки IIS.</span><span class="sxs-lookup"><span data-stu-id="34693-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="34693-244">Запуск проекта</span><span class="sxs-lookup"><span data-stu-id="34693-244">Run the project</span></span>

<span data-ttu-id="34693-245">Запустите Visual Studio от имени администратора.</span><span class="sxs-lookup"><span data-stu-id="34693-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="34693-246">Убедитесь, что для раскрывающегося списка с конфигурацией сборки построения выбрано значение **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="34693-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="34693-247">Настройте кнопку [Начать отладку](/visualstudio/debugger/debugger-feature-tour) на профиль **IIS** и нажмите ее для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="34693-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="34693-248">Если вы вошли в Visual Studio без прав администратора, возможно, потребуется перезапуск.</span><span class="sxs-lookup"><span data-stu-id="34693-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="34693-249">Перезапустите Visual Studio при появлении соответствующего запроса.</span><span class="sxs-lookup"><span data-stu-id="34693-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="34693-250">Если используется сертификат разработки без доверия, возможно, потребуется создать исключение для этого ненадежного сертификата по запросу в браузере.</span><span class="sxs-lookup"><span data-stu-id="34693-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="34693-251">Отладка конфигурации сборки выпуска с использованием функции [Только мой код](/visualstudio/debugger/just-my-code) и оптимизации компилятора приводит к ограничению возможностей.</span><span class="sxs-lookup"><span data-stu-id="34693-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="34693-252">Например, точки останова не будут достигнуты.</span><span class="sxs-lookup"><span data-stu-id="34693-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34693-253">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="34693-253">Additional resources</span></span>

* [<span data-ttu-id="34693-254">Начало работы с диспетчером IIS в IIS</span><span class="sxs-lookup"><span data-stu-id="34693-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
