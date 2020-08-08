---
title: Настройка внешнего входа в Twitter с помощью ASP.NET Core
author: rick-anderson
description: В этом руководстве демонстрируется интеграция аутентификации пользователя учетной записи Twitter с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: 977ab550dab0e654352f20264e7b032e063690e4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021826"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="5920e-103">Настройка внешнего входа в Twitter с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5920e-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="5920e-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="5920e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5920e-105">В этом примере показано, как разрешить пользователям [входить с помощью учетной записи Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) , используя пример проекта ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5920e-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="5920e-106">Создание приложения в Twitter</span><span class="sxs-lookup"><span data-stu-id="5920e-106">Create the app in Twitter</span></span>

* <span data-ttu-id="5920e-107">Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) .</span><span class="sxs-lookup"><span data-stu-id="5920e-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="5920e-108">Перейдите к [https://apps.twitter.com/](https://apps.twitter.com/) и войдите.</span><span class="sxs-lookup"><span data-stu-id="5920e-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="5920e-109">Если у вас еще нет учетной записи Twitter, используйте ссылку **[зарегистрироваться сейчас](https://twitter.com/signup)** , чтобы создать ее.</span><span class="sxs-lookup"><span data-stu-id="5920e-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="5920e-110">Выберите **создать приложение**.</span><span class="sxs-lookup"><span data-stu-id="5920e-110">Select **Create an app**.</span></span> <span data-ttu-id="5920e-111">Укажите **имя приложения**, **Описание приложения** и универсальный код ресурса (URI) общедоступного **веб-сайта** (это может быть временным, пока не будет зарегистрировано доменное имя):</span><span class="sxs-lookup"><span data-stu-id="5920e-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="5920e-112">Установите флажок **включить вход с помощью Twitter** .</span><span class="sxs-lookup"><span data-stu-id="5920e-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="5920e-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="5920e-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="5920e-114">требует, чтобы по умолчанию у пользователей был адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="5920e-114">requires users to have an email address by default.</span></span> <span data-ttu-id="5920e-115">Перейдите на вкладку **разрешения** , нажмите кнопку " **изменить** " и установите флажок " **запросить адрес электронной почты у пользователей**".</span><span class="sxs-lookup"><span data-stu-id="5920e-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="5920e-116">Введите URI разработки с `/signin-twitter` добавлением в поле **URL-адреса обратного вызова** (например, `https://webapp128.azurewebsites.net/signin-twitter` ).</span><span class="sxs-lookup"><span data-stu-id="5920e-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="5920e-117">Схема проверки подлинности Twitter, настроенная далее в этом примере, автоматически обрабатывает запросы по `/signin-twitter` маршруту для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="5920e-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="5920e-118">Сегмент URI `/signin-twitter` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Twitter.</span><span class="sxs-lookup"><span data-stu-id="5920e-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="5920e-119">URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Twitter с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [твиттероптионс](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="5920e-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="5920e-120">Заполните оставшуюся часть формы и выберите **создать**.</span><span class="sxs-lookup"><span data-stu-id="5920e-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="5920e-121">Отобразятся сведения о новом приложении:</span><span class="sxs-lookup"><span data-stu-id="5920e-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="5920e-122">Хранение ключа и секрета API потребителя Twitter</span><span class="sxs-lookup"><span data-stu-id="5920e-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="5920e-123">Храните конфиденциальные параметры, такие как ключ API потребителя Twitter и секретный код, с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5920e-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5920e-124">Для этого примера выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="5920e-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="5920e-125">Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="5920e-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="5920e-126">Храните конфиденциальные параметры в локальном хранилище секретов с помощью ключей секретов `Authentication:Twitter:ConsumerKey` и `Authentication:Twitter:ConsumerSecret` :</span><span class="sxs-lookup"><span data-stu-id="5920e-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5920e-127">Эти маркеры можно найти на вкладке **ключи и маркеры доступа** после создания нового приложения Twitter.</span><span class="sxs-lookup"><span data-stu-id="5920e-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="5920e-128">Настройка проверки подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="5920e-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="5920e-129">Добавьте службу Twitter в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="5920e-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="5920e-130">Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности в Twitter, см. в справочнике по API [твиттероптионс](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="5920e-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="5920e-131">Это можно использовать для запроса различных сведений о пользователе.</span><span class="sxs-lookup"><span data-stu-id="5920e-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="5920e-132">Вход с помощью Twitter</span><span class="sxs-lookup"><span data-stu-id="5920e-132">Sign in with Twitter</span></span>

<span data-ttu-id="5920e-133">Запустите приложение и выберите **Вход**.</span><span class="sxs-lookup"><span data-stu-id="5920e-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="5920e-134">Появится возможность входа с помощью Twitter:</span><span class="sxs-lookup"><span data-stu-id="5920e-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="5920e-135">При щелчке **Twitter** в Twitter перенаправления для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="5920e-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="5920e-136">После ввода учетных данных Twitter вы будете перенаправлены на веб-сайт, на котором можно задать свой адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="5920e-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="5920e-137">Теперь вы выполнили вход с использованием учетных данных Twitter:</span><span class="sxs-lookup"><span data-stu-id="5920e-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="5920e-138">Диагностика</span><span class="sxs-lookup"><span data-stu-id="5920e-138">Troubleshooting</span></span>

* <span data-ttu-id="5920e-139">**Только ASP.NET Core 2. x:** Если Identity параметр не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"*.</span><span class="sxs-lookup"><span data-stu-id="5920e-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="5920e-140">Шаблон проекта, используемый в этом образце, гарантирует, что это будет сделано.</span><span class="sxs-lookup"><span data-stu-id="5920e-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="5920e-141">Если база данных сайта не была создана с применением первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* .</span><span class="sxs-lookup"><span data-stu-id="5920e-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="5920e-142">Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.</span><span class="sxs-lookup"><span data-stu-id="5920e-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5920e-143">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="5920e-143">Next steps</span></span>

* <span data-ttu-id="5920e-144">В этой статье показано, как можно пройти проверку подлинности в Twitter.</span><span class="sxs-lookup"><span data-stu-id="5920e-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="5920e-145">Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5920e-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="5920e-146">После публикации веб-сайта в веб-приложение Azure необходимо сбросить настройки `ConsumerSecret` на портале разработчика Twitter.</span><span class="sxs-lookup"><span data-stu-id="5920e-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="5920e-147">Задайте `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` Параметры приложения и в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="5920e-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="5920e-148">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="5920e-148">The configuration system is set up to read keys from environment variables.</span></span>
