---
title: Внешняя установка входа Facebook в ASP.NET Core
author: rick-anderson
description: Учебник с примерами кода, демонстрирующим интеграцию аутентификации пользователя учетной записи Facebook в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277305"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="a2b51-103">Внешняя установка входа Facebook в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2b51-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="a2b51-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="a2b51-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="a2b51-105">В этом учебнике с примерами кода показано, как дать возможность пользователям войти в систему со своей учетной записью Facebook, используя образец ASP.NET проекта Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="a2b51-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="a2b51-106">Мы начинаем с создания Идентификатора приложения Facebook, следуя [официальным шагам.](https://developers.facebook.com)</span><span class="sxs-lookup"><span data-stu-id="a2b51-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="a2b51-107">Создать приложение на Facebook</span><span class="sxs-lookup"><span data-stu-id="a2b51-107">Create the app in Facebook</span></span>

* <span data-ttu-id="a2b51-108">Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a2b51-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="a2b51-109">Перейдите на страницу [приложения Facebook Developers](https://developers.facebook.com/apps/) и войдите в систему.</span><span class="sxs-lookup"><span data-stu-id="a2b51-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="a2b51-110">Если у вас еще нет учетной записи Facebook, используйте **ссылку на Facebook** на странице входа для ее создания.</span><span class="sxs-lookup"><span data-stu-id="a2b51-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="a2b51-111">Если у вас есть учетная запись Facebook, следуйте инструкциям, чтобы зарегистрироваться в качестве разработчика Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="a2b51-112">Из меню **My Apps** выберите **Создать приложение** для создания нового идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="a2b51-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook для разработчиков портал открыт в Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="a2b51-114">Заполните форму и нажмите кнопку **Create App ID.**</span><span class="sxs-lookup"><span data-stu-id="a2b51-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Создание новой формы идентификатора приложения](index/_static/FBNewAppId.png)

* <span data-ttu-id="a2b51-116">На новой карте Приложения выберите **Добавить продукт.**</span><span class="sxs-lookup"><span data-stu-id="a2b51-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="a2b51-117">На карте **Входа в Facebook** нажмите **«Настройка»**</span><span class="sxs-lookup"><span data-stu-id="a2b51-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Страница настройки продукта](index/_static/FBProductSetup.png)

* <span data-ttu-id="a2b51-119">Мастер **квикстарта** запускает с **выберите платформу** в качестве первой страницы.</span><span class="sxs-lookup"><span data-stu-id="a2b51-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="a2b51-120">Обойти мастера на данный момент, нажав **на FaceBook Логин** **Настройки** в меню в левом нижнем нижнем:</span><span class="sxs-lookup"><span data-stu-id="a2b51-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Перейти Быстрый старт](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="a2b51-122">Вам предоставляется страница **«Настройки клиента OAuth»:**</span><span class="sxs-lookup"><span data-stu-id="a2b51-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Страница Настройки клиента OAuth](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="a2b51-124">Введите свою разработку URI с */signin-facebook,* приложенное в `https://localhost:44320/signin-facebook`действительное поле **OAuth Redirect URIs** (например: ).</span><span class="sxs-lookup"><span data-stu-id="a2b51-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="a2b51-125">Проверка подлинности Facebook, настроенная позже в этом учебнике, будет автоматически обрабатывать запросы на *маршруте /signin-facebook* для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="a2b51-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="a2b51-126">URI */signin-facebook* установлен как обратный вызов по умолчанию поставщика аутентификации Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="a2b51-127">Вы можете изменить по умолчанию обратный вызов URI при настройке Facebook аутентификации промежуточного через унаследованные [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) собственности [класса FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="a2b51-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="a2b51-128">Нажмите кнопку **Сохранить изменения**.</span><span class="sxs-lookup"><span data-stu-id="a2b51-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="a2b51-129">Нажмите **Настройки** > **Основная** ссылка в левой навигации.</span><span class="sxs-lookup"><span data-stu-id="a2b51-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="a2b51-130">На этой странице, сделать `App ID` заметку вашего и вашего `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="a2b51-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="a2b51-131">Оба приложения ASP.NET Core в следующем разделе:</span><span class="sxs-lookup"><span data-stu-id="a2b51-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="a2b51-132">При развертывании сайта необходимо пересмотреть страницу установки **входа в Facebook** и зарегистрировать новую общедоступную URI.</span><span class="sxs-lookup"><span data-stu-id="a2b51-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="a2b51-133">Храните идентификатор идентификатор приложения Facebook и секрет</span><span class="sxs-lookup"><span data-stu-id="a2b51-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="a2b51-134">Храните чувствительные настройки, такие как идентификатор приложения Facebook и секретные значения с [помощью Secret Manager.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="a2b51-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a2b51-135">Для этого примера используйте следующие шаги:</span><span class="sxs-lookup"><span data-stu-id="a2b51-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="a2b51-136">Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="a2b51-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="a2b51-137">Храните чувствительные настройки в местном секретном магазине с секретными ключами `Authentication:Facebook:AppId` и: `Authentication:Facebook:AppSecret`</span><span class="sxs-lookup"><span data-stu-id="a2b51-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="a2b51-138">Настройка facebook аутентификация</span><span class="sxs-lookup"><span data-stu-id="a2b51-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="a2b51-139">Добавьте службу `ConfigureServices` Facebook в метод в *файле Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a2b51-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="a2b51-140">Вопиюте с Facebook</span><span class="sxs-lookup"><span data-stu-id="a2b51-140">Sign in with Facebook</span></span>

* <span data-ttu-id="a2b51-141">Запустите приложение и выберите **Войти в**.</span><span class="sxs-lookup"><span data-stu-id="a2b51-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="a2b51-142">**Под используйте другую службу для входа в систему.**, выберите Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="a2b51-143">Вы перенаправлены на **Facebook** для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a2b51-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="a2b51-144">Введите свои учетные данные на Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="a2b51-145">Вы перенаправлены обратно на ваш сайт, где вы можете настроить электронную почту.</span><span class="sxs-lookup"><span data-stu-id="a2b51-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="a2b51-146">Теперь вы вошли в систему, используя свои учетные данные Facebook:</span><span class="sxs-lookup"><span data-stu-id="a2b51-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="a2b51-147">Реагировать на отмену разрешения на внешний вйза</span><span class="sxs-lookup"><span data-stu-id="a2b51-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="a2b51-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>может обеспечить перенаправление агента пользователя, если пользователь не одобряет запрашиваемый запрос на авторизацию.</span><span class="sxs-lookup"><span data-stu-id="a2b51-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="a2b51-149">Следующий код `AccessDeniedPath` устанавливает: `"/AccessDeniedPathInfo"`</span><span class="sxs-lookup"><span data-stu-id="a2b51-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="a2b51-150">Мы рекомендуем, чтобы `AccessDeniedPath` страница содержала следующую информацию:</span><span class="sxs-lookup"><span data-stu-id="a2b51-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="a2b51-151">Удаленная аутентификация была отменена.</span><span class="sxs-lookup"><span data-stu-id="a2b51-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="a2b51-152">Это приложение требует проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a2b51-152">This app requires authentication.</span></span>
* <span data-ttu-id="a2b51-153">Чтобы попробовать войти снова, выберите ссылку входа.</span><span class="sxs-lookup"><span data-stu-id="a2b51-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="a2b51-154">Тест AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="a2b51-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="a2b51-155">Перейдите к [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="a2b51-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="a2b51-156">Если вы вписаны, вы должны выйти.</span><span class="sxs-lookup"><span data-stu-id="a2b51-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="a2b51-157">Запустите приложение и выберите Facebook войти в систему.</span><span class="sxs-lookup"><span data-stu-id="a2b51-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="a2b51-158">Выберите **не сейчас**.</span><span class="sxs-lookup"><span data-stu-id="a2b51-158">Select **Not now**.</span></span> <span data-ttu-id="a2b51-159">Вы перенаправляетсяе `AccessDeniedPath` на указанную страницу.</span><span class="sxs-lookup"><span data-stu-id="a2b51-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="a2b51-160">Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией Facebook, можно ознакомиться с ссылкой на [API Facebook.](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="a2b51-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="a2b51-161">Параметры конфигурации можно использовать для:</span><span class="sxs-lookup"><span data-stu-id="a2b51-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="a2b51-162">Запросить различную информацию о пользователе.</span><span class="sxs-lookup"><span data-stu-id="a2b51-162">Request different information about the user.</span></span>
* <span data-ttu-id="a2b51-163">Добавьте аргументы строки запроса, чтобы настроить опыт входа.</span><span class="sxs-lookup"><span data-stu-id="a2b51-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="a2b51-164">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="a2b51-164">Troubleshooting</span></span>

* <span data-ttu-id="a2b51-165">**ASP.NET core 2.x только:** Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.*</span><span class="sxs-lookup"><span data-stu-id="a2b51-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="a2b51-166">Шаблон проекта, используемый в этом учебнике, гарантирует, что это сделано.</span><span class="sxs-lookup"><span data-stu-id="a2b51-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="a2b51-167">Если база данных сайта не была создана при применении первоначальной миграции, *при обработке ошибки запроса операция базы данных сработала.*</span><span class="sxs-lookup"><span data-stu-id="a2b51-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="a2b51-168">Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.</span><span class="sxs-lookup"><span data-stu-id="a2b51-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a2b51-169">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="a2b51-169">Next steps</span></span>

* <span data-ttu-id="a2b51-170">Эта статья показала, как вы можете проверить подлинность с Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="a2b51-171">Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="a2b51-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="a2b51-172">Как только вы опубликуете свой веб-сайт в `AppSecret` веб-приложении Azure, вы должны сбросить на портале разработчиков Facebook.</span><span class="sxs-lookup"><span data-stu-id="a2b51-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="a2b51-173">Установите `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` настройки приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="a2b51-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="a2b51-174">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="a2b51-174">The configuration system is set up to read keys from environment variables.</span></span>
