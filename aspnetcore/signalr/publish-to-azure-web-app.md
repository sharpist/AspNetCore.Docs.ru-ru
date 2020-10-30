---
title: 'Публикация ASP.NET Core :::no-loc(SignalR)::: приложения в службе приложений Azure'
author: bradygaster
description: 'Узнайте, как опубликовать приложение ASP.NET Core :::no-loc(SignalR)::: в службе приложений Azure.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: e00eea81788c9b335691b7e5ffe6a46534c3c492
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058224"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="9b593-103">Публикация ASP.NET Core :::no-loc(SignalR)::: приложения в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="9b593-103">Publish an ASP.NET Core :::no-loc(SignalR)::: app to Azure App Service</span></span>

<span data-ttu-id="9b593-104">По [Брейди Гастер](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="9b593-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="9b593-105">[Служба приложений Azure](/azure/app-service/app-service-web-overview) — это служба платформы [облачных вычислений Майкрософт](https://azure.microsoft.com/) для размещения веб-приложений, включая ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b593-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9b593-106">В этой статье описывается публикация ASP.NET Core :::no-loc(SignalR)::: приложения из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b593-106">This article refers to publishing an ASP.NET Core :::no-loc(SignalR)::: app from Visual Studio.</span></span> <span data-ttu-id="9b593-107">Дополнительные сведения см. в статье [ :::no-loc(SignalR)::: служба для Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="9b593-107">For more information, see [:::no-loc(SignalR)::: service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="9b593-108">Публикация приложения</span><span class="sxs-lookup"><span data-stu-id="9b593-108">Publish the app</span></span>

<span data-ttu-id="9b593-109">В этой статье описывается публикация с помощью средств в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9b593-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="9b593-110">Visual Studio Code пользователи могут использовать команды [Azure CLI](/cli/azure) для публикации приложений в Azure.</span><span class="sxs-lookup"><span data-stu-id="9b593-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="9b593-111">Дополнительные сведения см. в статье [публикация ASP.NET Core приложения в Azure с помощью средств командной строки](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9b593-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="9b593-112">В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать** .</span><span class="sxs-lookup"><span data-stu-id="9b593-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="9b593-113">Убедитесь, что в диалоговом окне **Выбор целевого объекта публикации** выбран параметр **служба приложений** и **создать новые** .</span><span class="sxs-lookup"><span data-stu-id="9b593-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="9b593-114">Выберите **создать профиль** из раскрывающегося списка кнопка " **опубликовать** ".</span><span class="sxs-lookup"><span data-stu-id="9b593-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="9b593-115">Введите сведения, описанные в следующей таблице в диалоговом окне **Создание службы приложений** , и выберите **создать** .</span><span class="sxs-lookup"><span data-stu-id="9b593-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="9b593-116">Элемент</span><span class="sxs-lookup"><span data-stu-id="9b593-116">Item</span></span>               | <span data-ttu-id="9b593-117">Описание</span><span class="sxs-lookup"><span data-stu-id="9b593-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="9b593-118">**Имя**</span><span class="sxs-lookup"><span data-stu-id="9b593-118">**Name**</span></span>           | <span data-ttu-id="9b593-119">Уникальное имя приложения.</span><span class="sxs-lookup"><span data-stu-id="9b593-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="9b593-120">**Подписка**</span><span class="sxs-lookup"><span data-stu-id="9b593-120">**Subscription**</span></span>   | <span data-ttu-id="9b593-121">Подписка Azure, используемая приложением.</span><span class="sxs-lookup"><span data-stu-id="9b593-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="9b593-122">**Группа ресурсов**</span><span class="sxs-lookup"><span data-stu-id="9b593-122">**Resource Group**</span></span> | <span data-ttu-id="9b593-123">Группа связанных ресурсов, к которым принадлежит приложение.</span><span class="sxs-lookup"><span data-stu-id="9b593-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="9b593-124">**План размещения**</span><span class="sxs-lookup"><span data-stu-id="9b593-124">**Hosting Plan**</span></span>   | <span data-ttu-id="9b593-125">Тарифный план для веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="9b593-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="9b593-126">Выберите **:::no-loc(SignalR)::: службу Azure** в раскрывающемся списке **зависимости**  >  **Добавить** :</span><span class="sxs-lookup"><span data-stu-id="9b593-126">Select the **Azure :::no-loc(SignalR)::: Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Область зависимостей, отображающая выбор Azure::: No-Loc (SignalR)::: Service в раскрывающемся списке "Добавить"](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="9b593-128">В диалоговом окне **:::no-loc(SignalR)::: службы Azure** выберите **создать новый :::no-loc(SignalR)::: экземпляр службы Azure** .</span><span class="sxs-lookup"><span data-stu-id="9b593-128">In the **Azure :::no-loc(SignalR)::: Service** dialog, select **Create a new Azure :::no-loc(SignalR)::: Service instance** .</span></span>

1. <span data-ttu-id="9b593-129">Укажите **имя** , **группу ресурсов** и **Расположение** .</span><span class="sxs-lookup"><span data-stu-id="9b593-129">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="9b593-130">Вернитесь в диалоговое окно **:::no-loc(SignalR)::: службы Azure** и нажмите кнопку **добавить** .</span><span class="sxs-lookup"><span data-stu-id="9b593-130">Return to the **Azure :::no-loc(SignalR)::: Service** dialog and select **Add** .</span></span>

<span data-ttu-id="9b593-131">Visual Studio выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="9b593-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="9b593-132">Создает профиль публикации, содержащий параметры публикации.</span><span class="sxs-lookup"><span data-stu-id="9b593-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="9b593-133">Создает *веб-приложение Azure* с предоставленными сведениями.</span><span class="sxs-lookup"><span data-stu-id="9b593-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="9b593-134">Публикует приложение.</span><span class="sxs-lookup"><span data-stu-id="9b593-134">Publishes the app.</span></span>
* <span data-ttu-id="9b593-135">Запускает браузер, который загружает веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="9b593-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="9b593-136">URL-адрес приложения имеет формат `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="9b593-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="9b593-137">Например, приложение с именем `:::no-loc(SignalR):::ChatApp` имеет URL-адрес `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="9b593-137">For example, an app named `:::no-loc(SignalR):::ChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="9b593-138">Если при развертывании приложения, предназначенного для предварительной версии .NET Core, возникает ошибка HTTP *502,2-Bad Gateway* , см. статью [развертывание ASP.NET Core предварительной версии в службе приложений Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) .</span><span class="sxs-lookup"><span data-stu-id="9b593-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="9b593-139">Настройка приложения в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="9b593-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="9b593-140">*Этот раздел относится только к приложениям, которые не используют :::no-loc(SignalR)::: службу Azure.*</span><span class="sxs-lookup"><span data-stu-id="9b593-140">*This section only applies to apps not using the Azure :::no-loc(SignalR)::: Service.*</span></span>
>
> <span data-ttu-id="9b593-141">Если приложение использует :::no-loc(SignalR)::: службу Azure, служба приложений не требует настройки сходства маршрутизации запросов приложений (ARR) и веб-сокетов, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="9b593-141">If the app uses the Azure :::no-loc(SignalR)::: Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="9b593-142">Клиенты соединяют свои веб-сокеты со :::no-loc(SignalR)::: службой Azure, а не напрямую с приложением.</span><span class="sxs-lookup"><span data-stu-id="9b593-142">Clients connect their Web Sockets to the Azure :::no-loc(SignalR)::: Service, not directly to the app.</span></span>

<span data-ttu-id="9b593-143">Для приложений, размещенных без :::no-loc(SignalR)::: службы Azure, включите:</span><span class="sxs-lookup"><span data-stu-id="9b593-143">For apps hosted without the Azure :::no-loc(SignalR)::: Service, enable:</span></span>

* <span data-ttu-id="9b593-144">[Соответствие ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- :::no-loc(cookie)::: -(ARR- :::no-loc(cookie)::: ) -for-Azure-web-apps.html) для маршрутизации запросов от пользователя к тому же экземпляру службы приложений.</span><span class="sxs-lookup"><span data-stu-id="9b593-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-:::no-loc(cookie):::-(ARR-:::no-loc(cookie):::)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="9b593-145">Значение по умолчанию — **On** .</span><span class="sxs-lookup"><span data-stu-id="9b593-145">The default setting is **On** .</span></span>
* <span data-ttu-id="9b593-146">[Веб-сокеты](xref:fundamentals/websockets) для обеспечения функционирования транспорта веб-сокетов.</span><span class="sxs-lookup"><span data-stu-id="9b593-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="9b593-147">Значение по умолчанию — **Off** .</span><span class="sxs-lookup"><span data-stu-id="9b593-147">The default setting is **Off** .</span></span>

1. <span data-ttu-id="9b593-148">В портал Azure перейдите к веб-приложению в **службах приложений** .</span><span class="sxs-lookup"><span data-stu-id="9b593-148">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="9b593-149">Откройте **Configuration**  >  **Общие параметры** конфигурации.</span><span class="sxs-lookup"><span data-stu-id="9b593-149">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="9b593-150">Установите для **веб-сокетов** значение **вкл** .</span><span class="sxs-lookup"><span data-stu-id="9b593-150">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="9b593-151">Убедитесь, что для параметра **сходство arr** установлено значение **вкл** .</span><span class="sxs-lookup"><span data-stu-id="9b593-151">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="9b593-152">Ограничения плана службы приложений</span><span class="sxs-lookup"><span data-stu-id="9b593-152">App Service Plan limits</span></span>

<span data-ttu-id="9b593-153">Веб-сокеты и другие транспорты ограничены в зависимости от выбранного плана службы приложений.</span><span class="sxs-lookup"><span data-stu-id="9b593-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="9b593-154">Дополнительные сведения см. в разделе *ограничения облачных служб Azure* и *ограничения службы приложений* статьи [Подписка Azure и ограничения службы, квоты и ограничения](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="9b593-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b593-155">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9b593-155">Additional resources</span></span>

* [<span data-ttu-id="9b593-156">Что такое служба :::no-loc(SignalR)::: Azure?</span><span class="sxs-lookup"><span data-stu-id="9b593-156">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="9b593-157">Публикация приложения ASP.NET Core в Azure с использованием средств командной строки</span><span class="sxs-lookup"><span data-stu-id="9b593-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="9b593-158">Размещение и развертывание приложений ASP.NET Core Preview в Azure</span><span class="sxs-lookup"><span data-stu-id="9b593-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
