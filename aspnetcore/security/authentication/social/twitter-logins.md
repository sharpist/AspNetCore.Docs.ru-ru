---
title: Внешняя установка входной системы Twitter с ASP.NET Core
author: rick-anderson
description: Этот учебник демонстрирует интеграцию аутентификации пользователя учетной записи Twitter в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277292"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="d410e-103">Внешняя установка входной системы Twitter с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d410e-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="d410e-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="d410e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d410e-105">В этом примере показано, как дать возможность пользователям [войти в свою учетную запись Twitter с](https://dev.twitter.com/web/sign-in/desktop-browser) помощью образца ASP.NET проекта Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="d410e-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="d410e-106">Создать приложение в Твиттере</span><span class="sxs-lookup"><span data-stu-id="d410e-106">Create the app in Twitter</span></span>

* <span data-ttu-id="d410e-107">Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet.</span><span class="sxs-lookup"><span data-stu-id="d410e-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="d410e-108">Перейдите [https://apps.twitter.com/](https://apps.twitter.com/) и войти в систему.</span><span class="sxs-lookup"><span data-stu-id="d410e-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="d410e-109">Если у вас еще нет учетной записи Twitter, используйте ссылку **[Sign up для](https://twitter.com/signup)** ее создания.</span><span class="sxs-lookup"><span data-stu-id="d410e-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="d410e-110">Выберите **Создать приложение**.</span><span class="sxs-lookup"><span data-stu-id="d410e-110">Select **Create an app**.</span></span> <span data-ttu-id="d410e-111">Заполните **имя приложения**, **описание приложения** и общедоступный **веб-сайт** URI (это может быть временным, пока вы не зарегистрируете доменное имя):</span><span class="sxs-lookup"><span data-stu-id="d410e-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="d410e-112">Проверьте поле рядом **с включить войти в с Щебетать**</span><span class="sxs-lookup"><span data-stu-id="d410e-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="d410e-113">Microsoft.AspNetCore.Identity требует, чтобы пользователи имели адрес электронной почты по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d410e-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="d410e-114">Перейдите на вкладку **«Разрешения»,** нажмите кнопку **«Отображить»** и проверьте поле рядом с **адресом электронной почты Запроса от пользователей.**</span><span class="sxs-lookup"><span data-stu-id="d410e-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="d410e-115">Введите свой `/signin-twitter` URI развития с приложением к полю **URL-адресов Callback** (например: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="d410e-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="d410e-116">Схема аутентификации Twitter, настроенная позже `/signin-twitter` в этом примере, будет автоматически обрабатывать запросы на маршруте для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="d410e-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d410e-117">Сегмент `/signin-twitter` URI устанавливается как обратный вызов по умолчанию поставщика аутентификации Twitter.</span><span class="sxs-lookup"><span data-stu-id="d410e-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="d410e-118">Вы можете изменить по умолчанию обратный вызов URI при настройке Twitter аутентификации промежуточного через унаследованные [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) собственности [класса TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="d410e-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="d410e-119">Заполните оставшуюся форму и выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d410e-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="d410e-120">Отображаются новые сведения о приложении:</span><span class="sxs-lookup"><span data-stu-id="d410e-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="d410e-121">Храните ключ API-клавишу и секрет потребителя Twitter</span><span class="sxs-lookup"><span data-stu-id="d410e-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="d410e-122">Храните чувствительные настройки, такие как ключ API-исекретный ключ Twitter потребителя и секрет с [Secret Manager.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="d410e-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d410e-123">Для этого примера используйте следующие шаги:</span><span class="sxs-lookup"><span data-stu-id="d410e-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="d410e-124">Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="d410e-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="d410e-125">Храните чувствительные настройки в местном секретном магазине с ключами `Authentication:Twitter:ConsumerKey` секретов и: `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="d410e-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d410e-126">Эти маркеры можно найти на вкладке **«Ключи и токены доступа»** после создания нового приложения Twitter:</span><span class="sxs-lookup"><span data-stu-id="d410e-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="d410e-127">Настройка twitter аутентификации</span><span class="sxs-lookup"><span data-stu-id="d410e-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="d410e-128">Добавьте службу `ConfigureServices` Twitter в метод в *Startup.cs* файле:</span><span class="sxs-lookup"><span data-stu-id="d410e-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d410e-129">Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией Twitter, можно ознакомиться с ссылкой на [API Twitter.](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="d410e-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="d410e-130">Это может быть использовано для запроса различной информации о пользователе.</span><span class="sxs-lookup"><span data-stu-id="d410e-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="d410e-131">Вопиюте с Twitter</span><span class="sxs-lookup"><span data-stu-id="d410e-131">Sign in with Twitter</span></span>

<span data-ttu-id="d410e-132">Запустите приложение и выберите **Войти в**.</span><span class="sxs-lookup"><span data-stu-id="d410e-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="d410e-133">Появляется опция для вху-на-Айва с Twitter:</span><span class="sxs-lookup"><span data-stu-id="d410e-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="d410e-134">Нажав на **Twitter** перенаправляет на Twitter для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="d410e-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="d410e-135">После ввода учетных данных Twitter, вы перенаправитесь обратно на веб-сайт, где вы можете настроить электронную почту.</span><span class="sxs-lookup"><span data-stu-id="d410e-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="d410e-136">Теперь вы вошли в систему с помощью учетных данных Twitter:</span><span class="sxs-lookup"><span data-stu-id="d410e-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="d410e-137">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="d410e-137">Troubleshooting</span></span>

* <span data-ttu-id="d410e-138">**ASP.NET core 2.x только:** Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.*</span><span class="sxs-lookup"><span data-stu-id="d410e-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d410e-139">Шаблон проекта, используемый в этом примере, гарантирует, что это сделано.</span><span class="sxs-lookup"><span data-stu-id="d410e-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="d410e-140">Если база данных сайта не была создана при применении первоначальной миграции, при *обработке ошибки запроса операция базы данных сработала.*</span><span class="sxs-lookup"><span data-stu-id="d410e-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d410e-141">Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.</span><span class="sxs-lookup"><span data-stu-id="d410e-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d410e-142">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="d410e-142">Next steps</span></span>

* <span data-ttu-id="d410e-143">Эта статья показала, как вы можете проверить подлинность с Twitter.</span><span class="sxs-lookup"><span data-stu-id="d410e-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="d410e-144">Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="d410e-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="d410e-145">Как только вы опубликуете свой веб-сайт в `ConsumerSecret` веб-приложении Azure, вы должны сбросить на портале разработчиков Twitter.</span><span class="sxs-lookup"><span data-stu-id="d410e-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="d410e-146">Установите `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` настройки приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="d410e-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d410e-147">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="d410e-147">The configuration system is set up to read keys from environment variables.</span></span>
