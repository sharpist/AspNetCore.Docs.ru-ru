---
title: Внешняя настройка входа в систему учетной записи Майкрософт с ASP.NET Core
author: rick-anderson
description: Этот пример демонстрирует интеграцию аутентификации пользователя учетной записи Майкрософт в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384047"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="77d43-103">Внешняя настройка входа в систему учетной записи Майкрософт с ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77d43-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="77d43-104">Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="77d43-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="77d43-105">В этом примере показано, как дать возможность пользователям войти в систему с их работой, школой или личной учетной записью Майкрософт с помощью проекта ASP.NET Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="77d43-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="77d43-106">Создание приложения на портале разработчиков Майкрософт</span><span class="sxs-lookup"><span data-stu-id="77d43-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="77d43-107">Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="77d43-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="77d43-108">Перейдите на портал Azure - Страница [регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908) и создайте или войдите в учетную запись Майкрософт:</span><span class="sxs-lookup"><span data-stu-id="77d43-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="77d43-109">Если у вас нет учетной записи Майкрософт, выберите **создать ее.**</span><span class="sxs-lookup"><span data-stu-id="77d43-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="77d43-110">После входе в систему вы перенаправываетесь на страницу **регистрации приложений:**</span><span class="sxs-lookup"><span data-stu-id="77d43-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="77d43-111">Выберите **новую регистрацию**</span><span class="sxs-lookup"><span data-stu-id="77d43-111">Select **New registration**</span></span>
* <span data-ttu-id="77d43-112">Введите **имя**.</span><span class="sxs-lookup"><span data-stu-id="77d43-112">Enter a **Name**.</span></span>
* <span data-ttu-id="77d43-113">Выберите опцию для **типов поддерживаемых учетных записей.**</span><span class="sxs-lookup"><span data-stu-id="77d43-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="77d43-114">Под **перенаправлением URI**введите URL-адрес разработки с `/signin-microsoft` придативаемым.</span><span class="sxs-lookup"><span data-stu-id="77d43-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="77d43-115">Например, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="77d43-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="77d43-116">Схема проверки подлинности Майкрософт, настроенная позднее в этом примере, будет автоматически обрабатывать запросы на `/signin-microsoft` маршруте для реализации потока OAuth.</span><span class="sxs-lookup"><span data-stu-id="77d43-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="77d43-117">Выберите **Регистр**</span><span class="sxs-lookup"><span data-stu-id="77d43-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="77d43-118">Создание секрета клиента</span><span class="sxs-lookup"><span data-stu-id="77d43-118">Create client secret</span></span>

* <span data-ttu-id="77d43-119">В левой панели выберите **Сертификаты & секреты.**</span><span class="sxs-lookup"><span data-stu-id="77d43-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="77d43-120">Под **секретами клиента**, выберите **Новый секрет клиента**</span><span class="sxs-lookup"><span data-stu-id="77d43-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="77d43-121">Добавьте описание секрета клиента.</span><span class="sxs-lookup"><span data-stu-id="77d43-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="77d43-122">Выберите кнопку **Добавить.**</span><span class="sxs-lookup"><span data-stu-id="77d43-122">Select the **Add** button.</span></span>

* <span data-ttu-id="77d43-123">Под **секретами клиента,** скопировать стоимость клиента секрет.</span><span class="sxs-lookup"><span data-stu-id="77d43-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="77d43-124">Сегмент `/signin-microsoft` URI устанавливается как обратный вызов по умолчанию поставщика аутентификации Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="77d43-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="77d43-125">Вы можете изменить URI вызова по умолчанию при настройке промежуточного программного обеспечения Microsoft по середине с помощью унаследованного [свойства RemoteAuthentication. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="77d43-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="77d43-126">Храните идентификатор клиента и секрет Microsoft</span><span class="sxs-lookup"><span data-stu-id="77d43-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="77d43-127">Храните чувствительные настройки, такие как идентификатор клиента Microsoft и секретные значения с [помощью Secret Manager.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="77d43-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="77d43-128">Для этого примера используйте следующие шаги:</span><span class="sxs-lookup"><span data-stu-id="77d43-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="77d43-129">Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="77d43-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="77d43-130">Храните чувствительные настройки в местном секретном магазине с секретными ключами `Authentication:Microsoft:ClientId` и: `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="77d43-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="77d43-131">Настройка аутентификации учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="77d43-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="77d43-132">Добавьте службу учетной записи Майкрософт в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="77d43-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="77d43-133">Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией учетной записи Майкрософт, см. [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="77d43-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="77d43-134">Это может быть использовано для запроса различной информации о пользователе.</span><span class="sxs-lookup"><span data-stu-id="77d43-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="77d43-135">Вопиюте в учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="77d43-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="77d43-136">Выполнить приложение и нажмите **Войти в**.</span><span class="sxs-lookup"><span data-stu-id="77d43-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="77d43-137">Появляется опция входним в Microsoft.</span><span class="sxs-lookup"><span data-stu-id="77d43-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="77d43-138">При нажатии на кнопку Майкрософт вы перенаправляются в корпорацию Майкрософт для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="77d43-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="77d43-139">После входа в учетную запись Майкрософт вам будет предложено получить доступ к вашей информации:</span><span class="sxs-lookup"><span data-stu-id="77d43-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="77d43-140">Нажмите **Да,** и вы будете перенаправлены обратно на веб-сайт, где вы можете установить электронную почту.</span><span class="sxs-lookup"><span data-stu-id="77d43-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="77d43-141">Теперь вы вошли в систему с помощью учетных данных Майкрософт:</span><span class="sxs-lookup"><span data-stu-id="77d43-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="77d43-142">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="77d43-142">Troubleshooting</span></span>

* <span data-ttu-id="77d43-143">Если поставщик учетной записи Майкрософт перенаправляет вас на строку на странице ошибки, `#` обратите внимание на параметры строки запроса об ошибке и строке запроса описания непосредственно после (хэштег) в Uri.</span><span class="sxs-lookup"><span data-stu-id="77d43-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="77d43-144">Хотя сообщение об ошибке, кажется, указывает на проблему с аутентификацией Майкрософт, наиболее распространенной причиной является то, что приложение Uri не соответствует ни одной из **URI,redirect,** указанных для **веб-платформы.**</span><span class="sxs-lookup"><span data-stu-id="77d43-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="77d43-145">Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.*</span><span class="sxs-lookup"><span data-stu-id="77d43-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="77d43-146">Шаблон проекта, используемый в этом примере, гарантирует, что это сделано.</span><span class="sxs-lookup"><span data-stu-id="77d43-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="77d43-147">Если база данных сайта не была создана при применении первоначальной миграции, при *обработке ошибки запроса операция базы данных сработала.*</span><span class="sxs-lookup"><span data-stu-id="77d43-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="77d43-148">Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.</span><span class="sxs-lookup"><span data-stu-id="77d43-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="77d43-149">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="77d43-149">Next steps</span></span>

* <span data-ttu-id="77d43-150">Эта статья показала, как вы можете проверить подлинность с Microsoft.</span><span class="sxs-lookup"><span data-stu-id="77d43-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="77d43-151">Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)</span><span class="sxs-lookup"><span data-stu-id="77d43-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="77d43-152">Как только вы опубликуете свой веб-сайт в веб-приложении Azure, создайте новые секреты клиента на портале разработчиков Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="77d43-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="77d43-153">Установите `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` настройки приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="77d43-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="77d43-154">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="77d43-154">The configuration system is set up to read keys from environment variables.</span></span>
