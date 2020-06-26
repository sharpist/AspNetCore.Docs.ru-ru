---
title: Настройка внешнего входа Facebook в ASP.NET Core
author: rick-anderson
description: Руководство с примерами кода, демонстрирующими интеграцию аутентификации пользователя с учетной записью Facebook с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: df91b6f324de70b8492ccf0aef74c9264c3e9711
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403954"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="e74fb-103">Настройка внешнего входа Facebook в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e74fb-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="e74fb-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="e74fb-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="e74fb-105">В этом учебнике с примерами кода показано, как включить вход пользователей с помощью учетной записи Facebook, используя пример проекта ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e74fb-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="e74fb-106">Начнем с создания идентификатора приложения Facebook, следуя [официальным действиям](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="e74fb-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="e74fb-107">Создание приложения в Facebook</span><span class="sxs-lookup"><span data-stu-id="e74fb-107">Create the app in Facebook</span></span>

* <span data-ttu-id="e74fb-108">Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .</span><span class="sxs-lookup"><span data-stu-id="e74fb-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="e74fb-109">Перейдите на страницу [приложения Facebook Developers](https://developers.facebook.com/apps/) и выполните вход.</span><span class="sxs-lookup"><span data-stu-id="e74fb-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="e74fb-110">Если у вас еще нет учетной записи Facebook, используйте ссылку **зарегистрироваться для Facebook** на странице входа, чтобы создать ее.</span><span class="sxs-lookup"><span data-stu-id="e74fb-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="e74fb-111">После получения учетной записи Facebook следуйте инструкциям по регистрации в качестве разработчика Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="e74fb-112">В меню **Мои приложения** выберите пункт **создать приложение** , чтобы создать новый идентификатор приложения.</span><span class="sxs-lookup"><span data-stu-id="e74fb-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Портал Facebook для разработчиков открыт в Microsoft ребр](index/_static/FBMyApps.png)

* <span data-ttu-id="e74fb-114">Заполните форму и нажмите кнопку **CREATE App ID (создать идентификатор приложения** ).</span><span class="sxs-lookup"><span data-stu-id="e74fb-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Создание новой формы идентификатора приложения](index/_static/FBNewAppId.png)

* <span data-ttu-id="e74fb-116">В карточке нового приложения выберите **Добавить продукт**.</span><span class="sxs-lookup"><span data-stu-id="e74fb-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="e74fb-117">На карточке **входа Facebook** щелкните **настроить** .</span><span class="sxs-lookup"><span data-stu-id="e74fb-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Страница «Настройка продукта»](index/_static/FBProductSetup.png)

* <span data-ttu-id="e74fb-119">Мастер **быстрого** запуска запускается с параметром **выбрать платформу** в качестве первой страницы.</span><span class="sxs-lookup"><span data-stu-id="e74fb-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="e74fb-120">Пропустите мастер, щелкнув ссылку **Параметры** **входа Facebook** в меню в левом нижнем углу экрана:</span><span class="sxs-lookup"><span data-stu-id="e74fb-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Пропустить Быстрое начало](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="e74fb-122">Отобразится страница **параметров OAuth клиента** :</span><span class="sxs-lookup"><span data-stu-id="e74fb-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Страница параметров OAuth клиента](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="e74fb-124">Введите универсальный код ресурса (URI) для разработки с */сигнин-фацебук* , добавленным в **допустимое поле URI перенаправления OAuth** (например: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="e74fb-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="e74fb-125">Проверка подлинности Facebook, настроенная далее в этом руководстве, автоматически обрабатывает запросы по маршруту */сигнин-фацебук* для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="e74fb-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="e74fb-126">URI */сигнин-фацебук* задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="e74fb-127">URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Facebook с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [фацебукоптионс](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="e74fb-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="e74fb-128">Щелкните **Сохранить изменения**.</span><span class="sxs-lookup"><span data-stu-id="e74fb-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="e74fb-129">Щелкните ссылку **Параметры**  >  **Базовая** ссылка в области навигации слева.</span><span class="sxs-lookup"><span data-stu-id="e74fb-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="e74fb-130">На этой странице запишите `App ID` и ваш `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="e74fb-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="e74fb-131">Вы добавите оба варианта в приложение ASP.NET Core в следующем разделе:</span><span class="sxs-lookup"><span data-stu-id="e74fb-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="e74fb-132">При развертывании сайта необходимо повторно посетить страницу настройки **имени входа Facebook** и зарегистрировать новый общедоступный URI.</span><span class="sxs-lookup"><span data-stu-id="e74fb-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="e74fb-133">Хранение идентификатора и секрета приложения Facebook</span><span class="sxs-lookup"><span data-stu-id="e74fb-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="e74fb-134">Храните конфиденциальные параметры, такие как идентификатор приложения Facebook и значения секрета, с помощью [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e74fb-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e74fb-135">Для этого примера выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e74fb-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="e74fb-136">Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="e74fb-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="e74fb-137">Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Facebook:AppId` и `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="e74fb-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="e74fb-138">Настройка проверки подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="e74fb-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="e74fb-139">Добавьте службу Facebook в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="e74fb-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="e74fb-140">Вход с помощью Facebook</span><span class="sxs-lookup"><span data-stu-id="e74fb-140">Sign in with Facebook</span></span>

* <span data-ttu-id="e74fb-141">Запустите приложение и выберите **Вход**.</span><span class="sxs-lookup"><span data-stu-id="e74fb-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="e74fb-142">В разделе **использовать другую службу для входа в систему**выберите Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="e74fb-143">Вы будете перенаправлены на **Facebook** для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e74fb-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="e74fb-144">Введите учетные данные Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="e74fb-145">Вы будете перенаправлены на сайт, где можно задать свой адрес электронной почты.</span><span class="sxs-lookup"><span data-stu-id="e74fb-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="e74fb-146">Теперь вы выполнили вход с использованием учетных данных Facebook:</span><span class="sxs-lookup"><span data-stu-id="e74fb-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="e74fb-147">Реакция на отмену авторизации внешнего входа</span><span class="sxs-lookup"><span data-stu-id="e74fb-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="e74fb-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>может предоставить путь перенаправления к агенту пользователя, когда пользователь не утвердит запрошенный запрос на авторизацию.</span><span class="sxs-lookup"><span data-stu-id="e74fb-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="e74fb-149">Следующий код задает для значение `AccessDeniedPath` `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="e74fb-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="e74fb-150">Рекомендуется, чтобы `AccessDeniedPath` страница содержала следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="e74fb-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="e74fb-151">Удаленная проверка подлинности была отменена.</span><span class="sxs-lookup"><span data-stu-id="e74fb-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="e74fb-152">Для этого приложения требуется проверка подлинности.</span><span class="sxs-lookup"><span data-stu-id="e74fb-152">This app requires authentication.</span></span>
* <span data-ttu-id="e74fb-153">Чтобы повторить попытку входа, выберите ссылку для входа.</span><span class="sxs-lookup"><span data-stu-id="e74fb-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="e74fb-154">Тестовый Акцессдениедпас</span><span class="sxs-lookup"><span data-stu-id="e74fb-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="e74fb-155">Перейдите по адресу [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="e74fb-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="e74fb-156">Если вы вошли в систему, необходимо выйти из нее.</span><span class="sxs-lookup"><span data-stu-id="e74fb-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="e74fb-157">Запустите приложение и выберите пункт Вход в Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="e74fb-158">Выберите **не сейчас**.</span><span class="sxs-lookup"><span data-stu-id="e74fb-158">Select **Not now**.</span></span> <span data-ttu-id="e74fb-159">Вы будете перенаправлены на указанную `AccessDeniedPath` страницу.</span><span class="sxs-lookup"><span data-stu-id="e74fb-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="e74fb-160">Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности Facebook, см. в справочнике по API [фацебукоптионс](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="e74fb-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="e74fb-161">Параметры конфигурации можно использовать для:</span><span class="sxs-lookup"><span data-stu-id="e74fb-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="e74fb-162">Запросите другие сведения о пользователе.</span><span class="sxs-lookup"><span data-stu-id="e74fb-162">Request different information about the user.</span></span>
* <span data-ttu-id="e74fb-163">Добавьте аргументы строки запроса, чтобы настроить процедуру входа.</span><span class="sxs-lookup"><span data-stu-id="e74fb-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e74fb-164">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="e74fb-164">Troubleshooting</span></span>

* <span data-ttu-id="e74fb-165">**Только ASP.NET Core 2. x:** Если Identity параметр не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"*.</span><span class="sxs-lookup"><span data-stu-id="e74fb-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e74fb-166">Шаблон проекта, используемый в этом руководстве, гарантирует, что это будет сделано.</span><span class="sxs-lookup"><span data-stu-id="e74fb-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="e74fb-167">Если база данных сайта не была создана путем применения первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* .</span><span class="sxs-lookup"><span data-stu-id="e74fb-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e74fb-168">Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.</span><span class="sxs-lookup"><span data-stu-id="e74fb-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e74fb-169">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="e74fb-169">Next steps</span></span>

* <span data-ttu-id="e74fb-170">В этой статье показано, как можно пройти проверку подлинности с помощью Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="e74fb-171">Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e74fb-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="e74fb-172">После публикации веб-сайта в веб-приложение Azure необходимо сбросить его на `AppSecret` портале разработчика Facebook.</span><span class="sxs-lookup"><span data-stu-id="e74fb-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="e74fb-173">Задайте `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` Параметры приложения и в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="e74fb-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e74fb-174">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e74fb-174">The configuration system is set up to read keys from environment variables.</span></span>
