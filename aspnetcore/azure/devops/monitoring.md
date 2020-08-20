---
title: Мониторинг и отладка — DevOps с помощью ASP.NET Core и Azure
author: CamSoper
description: Мониторинг и отладка кода в рамках решения DevOps с помощью ASP.NET Core и Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
no-loc:
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
uid: azure/devops/monitor
ms.openlocfilehash: d5d5189563760cb8a61c188436caa3838e75a31e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626913"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="dfe45-103">Мониторинг и отладка</span><span class="sxs-lookup"><span data-stu-id="dfe45-103">Monitor and debug</span></span>

<span data-ttu-id="dfe45-104">После развертывания приложения и создания конвейера DevOps важно понять, как отслеживать и устранять неполадки в приложении.</span><span class="sxs-lookup"><span data-stu-id="dfe45-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="dfe45-105">В этом разделе вам предстоит выполнить следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="dfe45-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="dfe45-106">Поиск основных сведений о мониторинге и устранении неполадок на портале Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="dfe45-107">Изучение того, как Azure Monitor обеспечивает более подробный обзор метрик по всем службам Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="dfe45-108">Подключение веб-приложения к Application Insights для профилирования приложений</span><span class="sxs-lookup"><span data-stu-id="dfe45-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="dfe45-109">Включение ведения журнала и изучение места загрузки журналов</span><span class="sxs-lookup"><span data-stu-id="dfe45-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="dfe45-110">Потоковая передача журналов в режиме реального времени</span><span class="sxs-lookup"><span data-stu-id="dfe45-110">Stream logs in real time</span></span>
* <span data-ttu-id="dfe45-111">Изучение порядка настройки оповещений</span><span class="sxs-lookup"><span data-stu-id="dfe45-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="dfe45-112">Получение сведений об удаленной отладке веб-приложений Службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="dfe45-113">Базовый мониторинг и устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="dfe45-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="dfe45-114">Веб-приложения Службы приложений можно с легкостью отслеживать в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="dfe45-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="dfe45-115">На портале Azure метрики представлены в виде простых и понятных графиков и диаграмм.</span><span class="sxs-lookup"><span data-stu-id="dfe45-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="dfe45-116">На [портале Azure](https://portal.azure.com) перейдите к Службе приложений *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="dfe45-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="dfe45-117">На вкладке **Обзор** представлены полезные краткие сведения, включая графики недавних метрик.</span><span class="sxs-lookup"><span data-stu-id="dfe45-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Снимок экрана с разделом общих сведений](./media/monitoring/overview.png)

    * <span data-ttu-id="dfe45-119">**HTTP 5xx**: количество ошибок на стороне сервера (как правило, это исключения в коде ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="dfe45-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="dfe45-120">**Входные данные**: данные, поступающие в веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="dfe45-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="dfe45-121">**Выходные данные**: данные, исходящие из веб-приложения на клиенты.</span><span class="sxs-lookup"><span data-stu-id="dfe45-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="dfe45-122">**Запросы**: количество выполненных HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="dfe45-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="dfe45-123">**Среднее время ответа**: среднее время, в течение которого веб-приложение отвечает на HTTP-запросы.</span><span class="sxs-lookup"><span data-stu-id="dfe45-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="dfe45-124">На этой странице также представлено несколько средств для самостоятельного устранения неполадок и оптимизации.</span><span class="sxs-lookup"><span data-stu-id="dfe45-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Снимок экрана, на котором показаны инструменты для самостоятельного обслуживания](./media/monitoring/wizards.png)

    * <span data-ttu-id="dfe45-126">**Диагностика и решение проблем** — средство для самостоятельного устранения неполадок.</span><span class="sxs-lookup"><span data-stu-id="dfe45-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="dfe45-127">**Application Insights** — средство для профилирования производительности и поведения приложений (рассматривается далее в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="dfe45-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="dfe45-128">**Помощник по службе приложений** — предлагает рекомендации по настройке работы приложения.</span><span class="sxs-lookup"><span data-stu-id="dfe45-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="dfe45-129">Расширенный мониторинг</span><span class="sxs-lookup"><span data-stu-id="dfe45-129">Advanced monitoring</span></span>

<span data-ttu-id="dfe45-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) — это централизованная служба для мониторинга всех метрик и настройки оповещений в службах Azure.</span><span class="sxs-lookup"><span data-stu-id="dfe45-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="dfe45-131">В Azure Monitor администраторы могут подробно отслеживать производительность и выявлять тенденции.</span><span class="sxs-lookup"><span data-stu-id="dfe45-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="dfe45-132">Каждая служба Azure предлагает собственный [набор метрик](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) для Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="dfe45-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="dfe45-133">Профилирование с помощью Application Insights</span><span class="sxs-lookup"><span data-stu-id="dfe45-133">Profile with Application Insights</span></span>

<span data-ttu-id="dfe45-134">[Application Insights](/azure/application-insights/app-insights-overview) — это служба Azure для анализа производительности и стабильности веб-приложений, а также их использования.</span><span class="sxs-lookup"><span data-stu-id="dfe45-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="dfe45-135">Данные, получаемые из Application Insights, являются более широкими и более глубокими, чем в Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="dfe45-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="dfe45-136">Эти данные могут предоставить разработчикам и администраторам ключевые сведения для улучшения приложений.</span><span class="sxs-lookup"><span data-stu-id="dfe45-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="dfe45-137">Application Insights можно добавить в ресурс Службы приложений Azure без изменения кода.</span><span class="sxs-lookup"><span data-stu-id="dfe45-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="dfe45-138">На [портале Azure](https://portal.azure.com) перейдите к Службе приложений *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="dfe45-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="dfe45-139">На вкладке **Обзор** щелкните плитку **Application Insights**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Плитка Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="dfe45-141">Установите переключатель **Создать новый ресурс**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="dfe45-142">Используйте имя ресурса по умолчанию и выберите расположение ресурса Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dfe45-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="dfe45-143">Расположение не должно совпадать с расположением веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="dfe45-143">The location doesn't need to match that of your web app.</span></span>

    ![Настройка Application Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="dfe45-145">В раскрывающемся списке **Среда выполнения или платформа** выберите **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="dfe45-146">Примите настройки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dfe45-146">Accept the default settings.</span></span>
1. <span data-ttu-id="dfe45-147">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-147">Select **OK**.</span></span> <span data-ttu-id="dfe45-148">При появлении запроса на подтверждение выберите **Продолжить**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="dfe45-149">После создания ресурса щелкните имя ресурса Application Insights, чтобы перейти на страницу Application Insights.</span><span class="sxs-lookup"><span data-stu-id="dfe45-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Новый ресурс Application Insights готов](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="dfe45-151">По мере использования приложения накапливаются данные о работе с ним.</span><span class="sxs-lookup"><span data-stu-id="dfe45-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="dfe45-152">Выберите **Обновить**, чтобы перезагрузить колонку с учетом новых данных.</span><span class="sxs-lookup"><span data-stu-id="dfe45-152">Select **Refresh** to reload the blade with new data.</span></span>

![Вкладка "Обзор" в Application Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="dfe45-154">Application Insights предоставляет полезные сведения о сервере без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="dfe45-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="dfe45-155">Чтобы максимально эффективно использовать Application Insights, [следует инструментировать приложение с помощью пакета SDK для Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="dfe45-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="dfe45-156">При правильной настройке служба обеспечивает комплексный мониторинг веб-сервера и браузера, включая производительность на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="dfe45-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="dfe45-157">Дополнительные сведения см. в [документации по Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="dfe45-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="dfe45-158">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="dfe45-158">Logging</span></span>

<span data-ttu-id="dfe45-159">Журналы веб-сервера и приложений по умолчанию отключены в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="dfe45-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="dfe45-160">Включите их, выполнив следующие действия.</span><span class="sxs-lookup"><span data-stu-id="dfe45-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="dfe45-161">На [портале Azure](https://portal.azure.com) перейдите к Службе приложений *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="dfe45-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="dfe45-162">Прокрутите меню слева вниз до раздела **Мониторинг**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="dfe45-163">Выберите **Журнал диагностики**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-163">Select **Diagnostics logs**.</span></span>

    ![Ссылка "Журналы диагностики"](./media/monitoring/logging.png)

1. <span data-ttu-id="dfe45-165">Включите параметр **Ведение журнала приложений (файловая система)** .</span><span class="sxs-lookup"><span data-stu-id="dfe45-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="dfe45-166">При появлении запроса щелкните соответствующее поле установки расширения, чтобы включить ведение журнала приложений в веб-приложении.</span><span class="sxs-lookup"><span data-stu-id="dfe45-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="dfe45-167">Для параметра **Ведение журнала веб-сервера** выберите значение **Файловая система**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="dfe45-168">В поле **Срок хранения** укажите период хранения в днях.</span><span class="sxs-lookup"><span data-stu-id="dfe45-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="dfe45-169">Например, 30.</span><span class="sxs-lookup"><span data-stu-id="dfe45-169">For example, 30.</span></span>
1. <span data-ttu-id="dfe45-170">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-170">Click **Save**.</span></span>

<span data-ttu-id="dfe45-171">Для веб-приложения создаются журналы ASP.NET Core и журналы веб-сервера (Службы приложений).</span><span class="sxs-lookup"><span data-stu-id="dfe45-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="dfe45-172">Их можно загрузить с помощью отображаемых сведений о FTP/FTPS.</span><span class="sxs-lookup"><span data-stu-id="dfe45-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="dfe45-173">В качестве пароля используется пароль учетных данных развертывания, которые созданы ранее в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="dfe45-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="dfe45-174">Можно [выполнять потоковую передачу журналов прямо на локальный компьютер с помощью PowerShell или Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="dfe45-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="dfe45-175">Журналы также можно [просматривать в Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="dfe45-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="dfe45-176">Потоковая передача журналов</span><span class="sxs-lookup"><span data-stu-id="dfe45-176">Log streaming</span></span>

<span data-ttu-id="dfe45-177">Можно в режиме реального времени выполнять потоковую передачу журналов приложений и веб-сервера на портал.</span><span class="sxs-lookup"><span data-stu-id="dfe45-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="dfe45-178">На [портале Azure](https://portal.azure.com) перейдите к Службе приложений *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="dfe45-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="dfe45-179">Прокрутите меню слева вниз до раздела **Мониторинг** и выберите **Потоковая передача журнала**.</span><span class="sxs-lookup"><span data-stu-id="dfe45-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Снимок экрана со ссылкой "Потоковая передача журнала"](./media/monitoring/log-stream.png)

<span data-ttu-id="dfe45-181">Также можно [выполнять потоковую передачу журналов через Azure CLI или Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), а также через Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="dfe45-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="dfe45-182">Предупреждения</span><span class="sxs-lookup"><span data-stu-id="dfe45-182">Alerts</span></span>

<span data-ttu-id="dfe45-183">Azure Monitor также предоставляет [оповещения в режиме реального времени](/azure/monitoring-and-diagnostics/insights-alerts-portal) на основе метрик, событий администрирования и других критериев.</span><span class="sxs-lookup"><span data-stu-id="dfe45-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="dfe45-184">*Примечание. В настоящее время оповещение о метриках веб-приложения доступно только в службе "Оповещения (классические)".*</span><span class="sxs-lookup"><span data-stu-id="dfe45-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="dfe45-185">Служба [Оповещения (классические)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) находится в Azure Monitor или в разделе **Мониторинг** параметров Службы приложений.</span><span class="sxs-lookup"><span data-stu-id="dfe45-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Ссылка "Оповещения (классические)"](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="dfe45-187">Динамическая отладка</span><span class="sxs-lookup"><span data-stu-id="dfe45-187">Live debugging</span></span>

<span data-ttu-id="dfe45-188">Если в журналах недостаточно сведений, отладку Службы приложений Azure можно выполнять [удаленно с помощью Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug).</span><span class="sxs-lookup"><span data-stu-id="dfe45-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="dfe45-189">Однако для удаленной отладки приложение должно быть скомпилировано с отладочными символами.</span><span class="sxs-lookup"><span data-stu-id="dfe45-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="dfe45-190">Отладка не должна выполняться в рабочей среде, за исключением крайней необходимости.</span><span class="sxs-lookup"><span data-stu-id="dfe45-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="dfe45-191">Заключение</span><span class="sxs-lookup"><span data-stu-id="dfe45-191">Conclusion</span></span>

<span data-ttu-id="dfe45-192">В этом разделе вы выполнили следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="dfe45-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="dfe45-193">Поиск основных сведений о мониторинге и устранении неполадок на портале Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="dfe45-194">Изучение того, как Azure Monitor обеспечивает более подробный обзор метрик по всем службам Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="dfe45-195">Подключение веб-приложения к Application Insights для профилирования приложений</span><span class="sxs-lookup"><span data-stu-id="dfe45-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="dfe45-196">Включение ведения журнала и изучение места загрузки журналов</span><span class="sxs-lookup"><span data-stu-id="dfe45-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="dfe45-197">Потоковая передача журналов в режиме реального времени</span><span class="sxs-lookup"><span data-stu-id="dfe45-197">Stream logs in real time</span></span>
* <span data-ttu-id="dfe45-198">Изучение порядка настройки оповещений</span><span class="sxs-lookup"><span data-stu-id="dfe45-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="dfe45-199">Получение сведений об удаленной отладке веб-приложений Службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="dfe45-200">Дополнительные материалы для чтения</span><span class="sxs-lookup"><span data-stu-id="dfe45-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="dfe45-201">Мониторинг производительности веб-приложения Azure с помощью Application Insights</span><span class="sxs-lookup"><span data-stu-id="dfe45-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="dfe45-202">Включение функции ведения журналов диагностики для веб-приложений в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="dfe45-203">Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dfe45-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="dfe45-204">Создание классических оповещений метрик в Azure Monitor для служб Azure с помощью портала Azure</span><span class="sxs-lookup"><span data-stu-id="dfe45-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
