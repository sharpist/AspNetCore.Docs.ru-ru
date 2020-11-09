---
title: Настройка внешнего входа учетной записи Майкрософт с ASP.NET Core
author: rick-anderson
description: В этом примере демонстрируется интеграция учетная запись Майкрософт проверки подлинности пользователей в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060304"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="c6c56-103">Настройка внешнего входа учетной записи Майкрософт с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6c56-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="c6c56-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="c6c56-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c6c56-105">В этом примере показано, как разрешить пользователям выполнять вход с помощью рабочей, учебной или личной учетная запись Майкрософт с помощью проекта ASP.NET Core 3,0, созданного на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c6c56-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="c6c56-106">Создание приложения на портале разработчика Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c6c56-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="c6c56-107">Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) .</span><span class="sxs-lookup"><span data-stu-id="c6c56-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="c6c56-108">Перейдите на страницу [портал Azure-регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) и создайте или войдите в учетная запись Майкрософт:</span><span class="sxs-lookup"><span data-stu-id="c6c56-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="c6c56-109">Если у вас нет учетная запись Майкрософт, выберите **создать** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-109">If you don't have a Microsoft account, select **Create one** .</span></span> <span data-ttu-id="c6c56-110">После входа вы будете перенаправлены на **Регистрация приложений** страницу:</span><span class="sxs-lookup"><span data-stu-id="c6c56-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="c6c56-111">Выбрать **новую регистрацию**</span><span class="sxs-lookup"><span data-stu-id="c6c56-111">Select **New registration**</span></span>
* <span data-ttu-id="c6c56-112">Введите **Имя** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-112">Enter a **Name** .</span></span>
* <span data-ttu-id="c6c56-113">Выберите вариант для **поддерживаемых типов учетных записей** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-113">Select an option for **Supported account types** .</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="c6c56-114">`MicrosoftAccount`Пакет поддерживает регистрацию приложений, созданных с помощью параметров "учетные записи в любом каталоге организации" или "учетные записи в любом организационном каталоге и учетных записях Майкрософт" по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="c6c56-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="c6c56-115">Чтобы использовать другие параметры, установите `AuthorizationEndpoint` и `TokenEndpoint` члены, `MicrosoftAccountOptions` используемые для инициализации проверки подлинности учетной записи Майкрософт на URL-адреса, отображаемые на странице **конечные точки** регистрации приложения после ее создания (доступ к которой можно получить, щелкнув конечные точки на странице **Обзор** ).</span><span class="sxs-lookup"><span data-stu-id="c6c56-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="c6c56-116">В разделе **URI перенаправления** введите URL-адрес разработки с `/signin-microsoft` добавлением.</span><span class="sxs-lookup"><span data-stu-id="c6c56-116">Under **Redirect URI** , enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="c6c56-117">Например, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="c6c56-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="c6c56-118">Схема проверки подлинности Майкрософт, настроенная далее в этом примере, автоматически обрабатывает запросы по `/signin-microsoft` маршруту для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="c6c56-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="c6c56-119">Нажмите кнопку **Зарегистрировать** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="c6c56-120">Создать секрет клиента</span><span class="sxs-lookup"><span data-stu-id="c6c56-120">Create client secret</span></span>

* <span data-ttu-id="c6c56-121">В области слева щелкните **Сертификаты и секреты** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-121">In the left pane, select **Certificates & secrets** .</span></span>
* <span data-ttu-id="c6c56-122">В разделе **секреты клиента** выберите **новый секрет клиента** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-122">Under **Client secrets** , select **New client secret**</span></span>

  * <span data-ttu-id="c6c56-123">Добавьте описание секрета клиента.</span><span class="sxs-lookup"><span data-stu-id="c6c56-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="c6c56-124">Нажмите кнопку **Добавить** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-124">Select the **Add** button.</span></span>

* <span data-ttu-id="c6c56-125">В разделе **секреты клиента** скопируйте значение секрета клиента.</span><span class="sxs-lookup"><span data-stu-id="c6c56-125">Under **Client secrets** , copy the value of the client secret.</span></span>

<span data-ttu-id="c6c56-126">Сегмент URI `/signin-microsoft` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c6c56-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="c6c56-127">URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Майкрософт с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [микрософтаккаунтоптионс](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="c6c56-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="c6c56-128">Хранение идентификатора и секрета клиента Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c6c56-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="c6c56-129">Храните конфиденциальные параметры, такие как идентификатор клиента (Майкрософт) и секретные значения, с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c6c56-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c6c56-130">Для этого примера выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="c6c56-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c6c56-131">Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c6c56-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c6c56-132">Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Microsoft:ClientId` и `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="c6c56-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="c6c56-133">Настройка проверки подлинности учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c6c56-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="c6c56-134">Добавьте службу учетных записей Майкрософт в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c6c56-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="c6c56-135">Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности учетной записи Майкрософт, см. в справочнике по API [микрософтаккаунтоптионс](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="c6c56-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="c6c56-136">Это можно использовать для запроса различных сведений о пользователе.</span><span class="sxs-lookup"><span data-stu-id="c6c56-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="c6c56-137">Учетная запись Войдите с помощью учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="c6c56-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="c6c56-138">Запустите приложение и нажмите кнопку **войти** .</span><span class="sxs-lookup"><span data-stu-id="c6c56-138">Run the app and click **Log in** .</span></span> <span data-ttu-id="c6c56-139">Появится возможность войти в систему с помощью Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c6c56-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="c6c56-140">Если щелкнуть Майкрософт, вы будете перенаправлены в корпорацию Майкрософт для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c6c56-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="c6c56-141">После входа с помощью учетной записи Майкрософт вам будет предложено предоставить приложению доступ к вашим сведениям:</span><span class="sxs-lookup"><span data-stu-id="c6c56-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="c6c56-142">Коснитесь кнопки **Да** , и вы будете перенаправлены на веб-сайт, на котором можно задать свой адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="c6c56-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c6c56-143">Теперь вы выполнили вход с использованием учетных данных Майкрософт:</span><span class="sxs-lookup"><span data-stu-id="c6c56-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="c6c56-144">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="c6c56-144">Troubleshooting</span></span>

* <span data-ttu-id="c6c56-145">Если поставщик учетных записей Майкрософт перенаправит вас на страницу ошибки входа, обратите внимание на заголовок ошибки и описание параметры строки запроса непосредственно после `#` (хэш-кода) в универсальном коде ресурса (URI).</span><span class="sxs-lookup"><span data-stu-id="c6c56-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="c6c56-146">Несмотря на то, что сообщение об ошибке указывает на проблему с проверкой подлинности Майкрософт, наиболее распространенной причиной является URI приложения, не совпадающий ни с одним из **URI перенаправления** , указанных для **веб-** платформы.</span><span class="sxs-lookup"><span data-stu-id="c6c56-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="c6c56-147">Если Identity параметр не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"* .</span><span class="sxs-lookup"><span data-stu-id="c6c56-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="c6c56-148">Шаблон проекта, используемый в этом образце, гарантирует, что это будет сделано.</span><span class="sxs-lookup"><span data-stu-id="c6c56-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="c6c56-149">Если база данных сайта не была создана с применением первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* .</span><span class="sxs-lookup"><span data-stu-id="c6c56-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c6c56-150">Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.</span><span class="sxs-lookup"><span data-stu-id="c6c56-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c6c56-151">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="c6c56-151">Next steps</span></span>

* <span data-ttu-id="c6c56-152">В этой статье показано, как можно пройти проверку подлинности в Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c6c56-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="c6c56-153">Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c6c56-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c6c56-154">После публикации веб-сайта в веб-приложение Azure создайте секретные данные клиента на портале разработчика Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="c6c56-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="c6c56-155">Задайте `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` Параметры приложения и в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="c6c56-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c6c56-156">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="c6c56-156">The configuration system is set up to read keys from environment variables.</span></span>
