---
title: Подтверждение учетной записи и восстановление пароля в ASP.NET Core
author: rick-anderson
description: Узнайте, как создать приложение ASP.NET Core с подтверждением электронной почты и сбросом пароля.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: 1156ddd2921afbfeccaf077ca29d267f8b1e844a
ms.sourcegitcommit: 3544941682869734ea0113e24e02ed0ec9e1a9ec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2020
ms.locfileid: "86464557"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="9c105-103">Подтверждение учетной записи и восстановление пароля в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c105-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="9c105-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Понант](https://github.com/Ponant)и [Джо аудетте](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="9c105-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="9c105-105">В этом руководстве показано, как создать приложение ASP.NET Core с подтверждением электронной почты и сбросом пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="9c105-106">Это руководство **не** является началом статьи.</span><span class="sxs-lookup"><span data-stu-id="9c105-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="9c105-107">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="9c105-107">You should be familiar with:</span></span>

* [<span data-ttu-id="9c105-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c105-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="9c105-109">Authentication</span><span class="sxs-lookup"><span data-stu-id="9c105-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="9c105-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9c105-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9c105-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9c105-111">Prerequisites</span></span>

[<span data-ttu-id="9c105-112">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="9c105-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="9c105-113">Создание и тестирование веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="9c105-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="9c105-114">Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="9c105-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="9c105-115">Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="9c105-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9c105-116">После регистрации вы будете перенаправлены на `/Identity/Account/RegisterConfirmation` страницу, содержащую ссылку для имитации подтверждения по электронной почте:</span><span class="sxs-lookup"><span data-stu-id="9c105-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="9c105-117">Выберите `Click here to confirm your account` ссылку.</span><span class="sxs-lookup"><span data-stu-id="9c105-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="9c105-118">Выберите ссылку для **входа** и выполните вход с теми же учетными данными.</span><span class="sxs-lookup"><span data-stu-id="9c105-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="9c105-119">Выберите `Hello YourEmail@provider.com!` ссылку, которая перенаправит вас на `/Identity/Account/Manage/PersonalData` страницу.</span><span class="sxs-lookup"><span data-stu-id="9c105-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="9c105-120">Выберите вкладку **личные данные** слева, а затем щелкните **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="9c105-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="9c105-121">Настройка поставщика электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-121">Configure an email provider</span></span>

<span data-ttu-id="9c105-122">В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9c105-123">Для отправки электронной почты требуется учетная запись SendGrid и ключ.</span><span class="sxs-lookup"><span data-stu-id="9c105-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9c105-124">Вы можете использовать другие поставщики электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-124">You can use other email providers.</span></span> <span data-ttu-id="9c105-125">Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу.</span><span class="sxs-lookup"><span data-stu-id="9c105-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9c105-126">Протокол SMTP трудно защитить и настроить правильно.</span><span class="sxs-lookup"><span data-stu-id="9c105-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9c105-127">Для учетной записи SendGrid может потребоваться [Добавить отправителя](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="9c105-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="9c105-128">Создайте класс для выборки ключа защищенной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9c105-129">Для этого примера создайте *Services/аусмессажесендероптионс. CS*:</span><span class="sxs-lookup"><span data-stu-id="9c105-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9c105-130">Настройка секретов пользователя SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c105-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9c105-131">Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9c105-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9c105-132">Пример:</span><span class="sxs-lookup"><span data-stu-id="9c105-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9c105-133">В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.</span><span class="sxs-lookup"><span data-stu-id="9c105-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9c105-134">Содержимое *secrets.js* файла не шифруется.</span><span class="sxs-lookup"><span data-stu-id="9c105-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9c105-135">В следующей разметке показано *secrets.js* файла.</span><span class="sxs-lookup"><span data-stu-id="9c105-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9c105-136">`SendGridKey`Значение было удалено.</span><span class="sxs-lookup"><span data-stu-id="9c105-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9c105-137">Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.</span><span class="sxs-lookup"><span data-stu-id="9c105-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9c105-138">Установка SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c105-138">Install SendGrid</span></span>

<span data-ttu-id="9c105-139">В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.</span><span class="sxs-lookup"><span data-stu-id="9c105-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9c105-140">Установите `SendGrid` пакет NuGet:</span><span class="sxs-lookup"><span data-stu-id="9c105-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9c105-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c105-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c105-142">В консоли диспетчера пакетов введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9c105-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9c105-143">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="9c105-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9c105-144">В консоли введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9c105-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9c105-145">Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.</span><span class="sxs-lookup"><span data-stu-id="9c105-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9c105-146">Реализация Иемаилсендер</span><span class="sxs-lookup"><span data-stu-id="9c105-146">Implement IEmailSender</span></span>

<span data-ttu-id="9c105-147">Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:</span><span class="sxs-lookup"><span data-stu-id="9c105-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9c105-148">Настройка запуска для поддержки электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-148">Configure startup to support email</span></span>

<span data-ttu-id="9c105-149">Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="9c105-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9c105-150">Добавьте `EmailSender` в качестве временной службы.</span><span class="sxs-lookup"><span data-stu-id="9c105-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9c105-151">Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.</span><span class="sxs-lookup"><span data-stu-id="9c105-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="9c105-152">Регистерконфирматион шаблонов</span><span class="sxs-lookup"><span data-stu-id="9c105-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="9c105-153">Следуйте инструкциям по [формированию Identity шаблонов](xref:security/authentication/scaffold-identity) и шаблонов `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="9c105-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9c105-154">Регистрация, подтверждение электронной почты и сброс пароля</span><span class="sxs-lookup"><span data-stu-id="9c105-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9c105-155">Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9c105-156">Запуск приложения и регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="9c105-156">Run the app and register a new user</span></span>
* <span data-ttu-id="9c105-157">Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9c105-158">Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .</span><span class="sxs-lookup"><span data-stu-id="9c105-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9c105-159">Щелкните ссылку, чтобы подтвердить свою электронную почту.</span><span class="sxs-lookup"><span data-stu-id="9c105-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9c105-160">Выполните вход, используя свой адрес электронной почты и пароль.</span><span class="sxs-lookup"><span data-stu-id="9c105-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="9c105-161">Выполните выход.</span><span class="sxs-lookup"><span data-stu-id="9c105-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9c105-162">Проверка сброса пароля</span><span class="sxs-lookup"><span data-stu-id="9c105-162">Test password reset</span></span>

* <span data-ttu-id="9c105-163">Если вы вошли в этот компьютер, выберите **выход**.</span><span class="sxs-lookup"><span data-stu-id="9c105-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9c105-164">Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .</span><span class="sxs-lookup"><span data-stu-id="9c105-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9c105-165">Введите адрес электронной почты, использованный для регистрации учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9c105-166">Отправляется сообщение электронной почты со ссылкой для сброса пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9c105-167">Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль.</span><span class="sxs-lookup"><span data-stu-id="9c105-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9c105-168">После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9c105-169">Изменить время ожидания для электронной почты и активности</span><span class="sxs-lookup"><span data-stu-id="9c105-169">Change email and activity timeout</span></span>

<span data-ttu-id="9c105-170">Время ожидания бездействия по умолчанию составляет 14 дней.</span><span class="sxs-lookup"><span data-stu-id="9c105-170">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9c105-171">Следующий код задает время ожидания простоя в 5 дней:</span><span class="sxs-lookup"><span data-stu-id="9c105-171">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9c105-172">Изменить все продолжительность существования маркеров защиты данных</span><span class="sxs-lookup"><span data-stu-id="9c105-172">Change all data protection token lifespans</span></span>

<span data-ttu-id="9c105-173">Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:</span><span class="sxs-lookup"><span data-stu-id="9c105-173">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="9c105-174">Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c105-174">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9c105-175">Изменение срока существования маркера электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-175">Change the email token lifespan</span></span>

<span data-ttu-id="9c105-176">Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c105-176">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9c105-177">В этом разделе показано, как изменить срок существования маркера электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-177">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9c105-178">Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9c105-178">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9c105-179">Добавьте настраиваемый поставщик в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="9c105-179">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9c105-180">Подтверждение повторной отправки электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-180">Resend email confirmation</span></span>

<span data-ttu-id="9c105-181">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9c105-181">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9c105-182">Отладка электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-182">Debug email</span></span>

<span data-ttu-id="9c105-183">Если вы не можете работать с электронной почтой:</span><span class="sxs-lookup"><span data-stu-id="9c105-183">If you can't get email working:</span></span>

* <span data-ttu-id="9c105-184">Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="9c105-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9c105-185">Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="9c105-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9c105-186">Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9c105-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9c105-187">Проверьте папку спама.</span><span class="sxs-lookup"><span data-stu-id="9c105-187">Check your spam folder.</span></span>
* <span data-ttu-id="9c105-188">Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).</span><span class="sxs-lookup"><span data-stu-id="9c105-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9c105-189">Попробуйте отправить их в другую учетную запись электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="9c105-190">В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке.</span><span class="sxs-lookup"><span data-stu-id="9c105-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9c105-191">Если приложение публикуется в Azure, задайте секреты SendGrid как параметры приложения на портале веб-приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="9c105-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9c105-192">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="9c105-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9c105-193">Объединение социальных и местных учетных записей входа</span><span class="sxs-lookup"><span data-stu-id="9c105-193">Combine social and local login accounts</span></span>

<span data-ttu-id="9c105-194">Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9c105-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9c105-195">См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9c105-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9c105-196">Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту.</span><span class="sxs-lookup"><span data-stu-id="9c105-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9c105-197">В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.</span><span class="sxs-lookup"><span data-stu-id="9c105-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

<span data-ttu-id="9c105-199">Щелкните ссылку **Управление** .</span><span class="sxs-lookup"><span data-stu-id="9c105-199">Click on the **Manage** link.</span></span> <span data-ttu-id="9c105-200">Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.</span><span class="sxs-lookup"><span data-stu-id="9c105-200">Note the 0 external (social logins) associated with this account.</span></span>

![Управление представлением](accconfirm/_static/manage.png)

<span data-ttu-id="9c105-202">Щелкните ссылку на другую службу входа и примите запросы приложения.</span><span class="sxs-lookup"><span data-stu-id="9c105-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9c105-203">На следующем рисунке Facebook является внешним поставщиком проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="9c105-203">In the following image, Facebook is the external authentication provider:</span></span>

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="9c105-205">Две учетные записи были объединены.</span><span class="sxs-lookup"><span data-stu-id="9c105-205">The two accounts have been combined.</span></span> <span data-ttu-id="9c105-206">Вы можете войти с помощью любой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-206">You are able to sign in with either account.</span></span> <span data-ttu-id="9c105-207">Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.</span><span class="sxs-lookup"><span data-stu-id="9c105-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9c105-208">Включить подтверждение учетной записи после того, как у сайта есть пользователи</span><span class="sxs-lookup"><span data-stu-id="9c105-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9c105-209">Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="9c105-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9c105-210">Существующие пользователи заблокированы, так как их учетные записи не подтверждены.</span><span class="sxs-lookup"><span data-stu-id="9c105-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9c105-211">Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="9c105-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9c105-212">Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.</span><span class="sxs-lookup"><span data-stu-id="9c105-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9c105-213">Подтвердите существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="9c105-213">Confirm existing users.</span></span> <span data-ttu-id="9c105-214">Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="9c105-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9c105-215">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="9c105-215">Prerequisites</span></span>

[<span data-ttu-id="9c105-216">Пакет SDK для .NET Core 2,2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="9c105-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="9c105-217">Создание веб-приложения и шаблонаIdentity</span><span class="sxs-lookup"><span data-stu-id="9c105-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="9c105-218">Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="9c105-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="9c105-219">Если <xref:Microsoft.AspNetCore.Identity.PasswordOptions> настроен в `Startup.ConfigureServices` , для свойства в шаблонных страницах может потребоваться настройка [ `[StringLength]` атрибутов](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity .</span><span class="sxs-lookup"><span data-stu-id="9c105-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="9c105-220">`InputModel` `Password` Свойство находится в `Areas/Identity/Pages/Account/Register.cshtml.cs` файле после формирования шаблонов Identity .</span><span class="sxs-lookup"><span data-stu-id="9c105-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="9c105-221">Тестирование регистрации нового пользователя</span><span class="sxs-lookup"><span data-stu-id="9c105-221">Test new user registration</span></span>

<span data-ttu-id="9c105-222">Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="9c105-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9c105-223">На этом этапе единственной проверкой адреса электронной почты является [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="9c105-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="9c105-224">После отправки регистрации вы вошли в приложение.</span><span class="sxs-lookup"><span data-stu-id="9c105-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="9c105-225">Далее в этом руководстве код обновляется, поэтому новые пользователи не смогут войти в систему, пока не будет проверена электронная почта.</span><span class="sxs-lookup"><span data-stu-id="9c105-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="9c105-226">Обратите внимание, что `EmailConfirmed` поле таблицы имеет значение `False` .</span><span class="sxs-lookup"><span data-stu-id="9c105-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="9c105-227">Вы можете снова использовать это сообщение на следующем шаге, когда приложение отправит сообщение электронной почты с подтверждением.</span><span class="sxs-lookup"><span data-stu-id="9c105-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="9c105-228">Щелкните строку правой кнопкой мыши и выберите команду **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="9c105-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="9c105-229">Удаление псевдонима электронной почты упрощает следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="9c105-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="9c105-230">Требовать подтверждение по электронной почте</span><span class="sxs-lookup"><span data-stu-id="9c105-230">Require email confirmation</span></span>

<span data-ttu-id="9c105-231">Рекомендуется подтвердить сообщение электронной почты о новой регистрации пользователя.</span><span class="sxs-lookup"><span data-stu-id="9c105-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="9c105-232">Подтверждение по электронной почте помогает проверить, что они не олицетворяют другого пользователя (т. е. они не зарегистрированы в сообщении электронной почты другого пользователя).</span><span class="sxs-lookup"><span data-stu-id="9c105-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="9c105-233">Предположим, у вас есть дискуссионный форум, и вы хотите предотвратить yli@example.com регистрацию "" в виде "" nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="9c105-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="9c105-234">Без подтверждения по электронной почте " nolivetto@contoso.com " может получить от приложения нежелательное сообщение электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="9c105-235">Предположим, что пользователь случайно зарегистрировался как " ylo@example.com " и не заметил опечатку "или".</span><span class="sxs-lookup"><span data-stu-id="9c105-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="9c105-236">Они не смогут использовать восстановление пароля, так как у приложения нет нужной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="9c105-237">Подтверждение по электронной почте обеспечивает ограниченную защиту от программы-роботы.</span><span class="sxs-lookup"><span data-stu-id="9c105-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="9c105-238">Подтверждение по электронной почте не обеспечивает защиту от вредоносных пользователей со многими учетными записями электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="9c105-239">Обычно требуется запретить новым пользователям отправлять данные на ваш веб-сайт, прежде чем они будут иметь подтвержденное сообщение электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="9c105-240">Обновление `Startup.ConfigureServices` для запроса подтвержденного адреса электронной почты:</span><span class="sxs-lookup"><span data-stu-id="9c105-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="9c105-241">`config.SignIn.RequireConfirmedEmail = true;`запрещает зарегистрированным пользователям входить в систему, пока не будет подтверждено их электронная почта.</span><span class="sxs-lookup"><span data-stu-id="9c105-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="9c105-242">Настройка поставщика электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-242">Configure email provider</span></span>

<span data-ttu-id="9c105-243">В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9c105-244">Для отправки электронной почты требуется учетная запись SendGrid и ключ.</span><span class="sxs-lookup"><span data-stu-id="9c105-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9c105-245">Вы можете использовать другие поставщики электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-245">You can use other email providers.</span></span> <span data-ttu-id="9c105-246">ASP.NET Core 2. x включает `System.Net.Mail` , что позволяет отправлять электронную почту из приложения.</span><span class="sxs-lookup"><span data-stu-id="9c105-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="9c105-247">Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу.</span><span class="sxs-lookup"><span data-stu-id="9c105-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9c105-248">Протокол SMTP трудно защитить и настроить правильно.</span><span class="sxs-lookup"><span data-stu-id="9c105-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9c105-249">Создайте класс для выборки ключа защищенной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9c105-250">Для этого примера создайте *Services/аусмессажесендероптионс. CS*:</span><span class="sxs-lookup"><span data-stu-id="9c105-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9c105-251">Настройка секретов пользователя SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c105-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9c105-252">Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9c105-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9c105-253">Пример:</span><span class="sxs-lookup"><span data-stu-id="9c105-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9c105-254">В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.</span><span class="sxs-lookup"><span data-stu-id="9c105-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9c105-255">Содержимое *secrets.js* файла не шифруется.</span><span class="sxs-lookup"><span data-stu-id="9c105-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9c105-256">В следующей разметке показано *secrets.js* файла.</span><span class="sxs-lookup"><span data-stu-id="9c105-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9c105-257">`SendGridKey`Значение было удалено.</span><span class="sxs-lookup"><span data-stu-id="9c105-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9c105-258">Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.</span><span class="sxs-lookup"><span data-stu-id="9c105-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9c105-259">Установка SendGrid</span><span class="sxs-lookup"><span data-stu-id="9c105-259">Install SendGrid</span></span>

<span data-ttu-id="9c105-260">В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.</span><span class="sxs-lookup"><span data-stu-id="9c105-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9c105-261">Установите `SendGrid` пакет NuGet:</span><span class="sxs-lookup"><span data-stu-id="9c105-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9c105-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c105-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9c105-263">В консоли диспетчера пакетов введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9c105-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9c105-264">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="9c105-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9c105-265">В консоли введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="9c105-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9c105-266">Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.</span><span class="sxs-lookup"><span data-stu-id="9c105-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9c105-267">Реализация Иемаилсендер</span><span class="sxs-lookup"><span data-stu-id="9c105-267">Implement IEmailSender</span></span>

<span data-ttu-id="9c105-268">Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:</span><span class="sxs-lookup"><span data-stu-id="9c105-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9c105-269">Настройка запуска для поддержки электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-269">Configure startup to support email</span></span>

<span data-ttu-id="9c105-270">Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="9c105-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9c105-271">Добавьте `EmailSender` в качестве временной службы.</span><span class="sxs-lookup"><span data-stu-id="9c105-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9c105-272">Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.</span><span class="sxs-lookup"><span data-stu-id="9c105-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="9c105-273">Включение подтверждения учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="9c105-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="9c105-274">Шаблон содержит код для подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="9c105-275">Найдите `OnPostAsync` метод в *области (Areas/ Identity /Пажес/аккаунт/регистер.кштмл.КС*).</span><span class="sxs-lookup"><span data-stu-id="9c105-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="9c105-276">Запретите автоматический вход новых зарегистрированных пользователей с помощью комментария к следующей строке:</span><span class="sxs-lookup"><span data-stu-id="9c105-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="9c105-277">Показан полный метод с выделенной измененной строкой:</span><span class="sxs-lookup"><span data-stu-id="9c105-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9c105-278">Регистрация, подтверждение электронной почты и сброс пароля</span><span class="sxs-lookup"><span data-stu-id="9c105-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9c105-279">Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9c105-280">Запуск приложения и регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="9c105-280">Run the app and register a new user</span></span>
* <span data-ttu-id="9c105-281">Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9c105-282">Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .</span><span class="sxs-lookup"><span data-stu-id="9c105-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9c105-283">Щелкните ссылку, чтобы подтвердить свою электронную почту.</span><span class="sxs-lookup"><span data-stu-id="9c105-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9c105-284">Выполните вход, используя свой адрес электронной почты и пароль.</span><span class="sxs-lookup"><span data-stu-id="9c105-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="9c105-285">Выполните выход.</span><span class="sxs-lookup"><span data-stu-id="9c105-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="9c105-286">Просмотр страницы "Управление"</span><span class="sxs-lookup"><span data-stu-id="9c105-286">View the manage page</span></span>

<span data-ttu-id="9c105-287">Выберите имя пользователя в браузере: ![ окно браузера с именем пользователя.](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="9c105-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="9c105-288">Страница Управление отображается с выбранной вкладкой **профиль** .</span><span class="sxs-lookup"><span data-stu-id="9c105-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="9c105-289">В **сообщении электронной почты** отображается флажок, указывающий, что сообщение электронной почты подтверждено.</span><span class="sxs-lookup"><span data-stu-id="9c105-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9c105-290">Проверка сброса пароля</span><span class="sxs-lookup"><span data-stu-id="9c105-290">Test password reset</span></span>

* <span data-ttu-id="9c105-291">Если вы вошли в этот компьютер, выберите **выход**.</span><span class="sxs-lookup"><span data-stu-id="9c105-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9c105-292">Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .</span><span class="sxs-lookup"><span data-stu-id="9c105-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9c105-293">Введите адрес электронной почты, использованный для регистрации учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9c105-294">Отправляется сообщение электронной почты со ссылкой для сброса пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9c105-295">Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль.</span><span class="sxs-lookup"><span data-stu-id="9c105-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9c105-296">После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.</span><span class="sxs-lookup"><span data-stu-id="9c105-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9c105-297">Изменить время ожидания для электронной почты и активности</span><span class="sxs-lookup"><span data-stu-id="9c105-297">Change email and activity timeout</span></span>

<span data-ttu-id="9c105-298">Время ожидания бездействия по умолчанию составляет 14 дней.</span><span class="sxs-lookup"><span data-stu-id="9c105-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9c105-299">Следующий код задает время ожидания простоя в 5 дней:</span><span class="sxs-lookup"><span data-stu-id="9c105-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9c105-300">Изменить все продолжительность существования маркеров защиты данных</span><span class="sxs-lookup"><span data-stu-id="9c105-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="9c105-301">Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:</span><span class="sxs-lookup"><span data-stu-id="9c105-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="9c105-302">Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c105-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9c105-303">Изменение срока существования маркера электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-303">Change the email token lifespan</span></span>

<span data-ttu-id="9c105-304">Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c105-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9c105-305">В этом разделе показано, как изменить срок существования маркера электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9c105-306">Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9c105-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9c105-307">Добавьте настраиваемый поставщик в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="9c105-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9c105-308">Подтверждение повторной отправки электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-308">Resend email confirmation</span></span>

<span data-ttu-id="9c105-309">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9c105-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9c105-310">Отладка электронной почты</span><span class="sxs-lookup"><span data-stu-id="9c105-310">Debug email</span></span>

<span data-ttu-id="9c105-311">Если вы не можете работать с электронной почтой:</span><span class="sxs-lookup"><span data-stu-id="9c105-311">If you can't get email working:</span></span>

* <span data-ttu-id="9c105-312">Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="9c105-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9c105-313">Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="9c105-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9c105-314">Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9c105-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9c105-315">Проверьте папку спама.</span><span class="sxs-lookup"><span data-stu-id="9c105-315">Check your spam folder.</span></span>
* <span data-ttu-id="9c105-316">Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).</span><span class="sxs-lookup"><span data-stu-id="9c105-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9c105-317">Попробуйте отправить их в другую учетную запись электронной почты.</span><span class="sxs-lookup"><span data-stu-id="9c105-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="9c105-318">В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке.</span><span class="sxs-lookup"><span data-stu-id="9c105-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9c105-319">При публикации приложения в Azure можно задать секреты SendGrid как параметры приложения на портале веб-приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="9c105-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9c105-320">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="9c105-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9c105-321">Объединение социальных и местных учетных записей входа</span><span class="sxs-lookup"><span data-stu-id="9c105-321">Combine social and local login accounts</span></span>

<span data-ttu-id="9c105-322">Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9c105-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9c105-323">См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9c105-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9c105-324">Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту.</span><span class="sxs-lookup"><span data-stu-id="9c105-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9c105-325">В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.</span><span class="sxs-lookup"><span data-stu-id="9c105-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

<span data-ttu-id="9c105-327">Щелкните ссылку **Управление** .</span><span class="sxs-lookup"><span data-stu-id="9c105-327">Click on the **Manage** link.</span></span> <span data-ttu-id="9c105-328">Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.</span><span class="sxs-lookup"><span data-stu-id="9c105-328">Note the 0 external (social logins) associated with this account.</span></span>

![Управление представлением](accconfirm/_static/manage.png)

<span data-ttu-id="9c105-330">Щелкните ссылку на другую службу входа и примите запросы приложения.</span><span class="sxs-lookup"><span data-stu-id="9c105-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9c105-331">На следующем рисунке Facebook является внешним поставщиком проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="9c105-331">In the following image, Facebook is the external authentication provider:</span></span>

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="9c105-333">Две учетные записи были объединены.</span><span class="sxs-lookup"><span data-stu-id="9c105-333">The two accounts have been combined.</span></span> <span data-ttu-id="9c105-334">Вы можете войти с помощью любой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="9c105-334">You are able to sign in with either account.</span></span> <span data-ttu-id="9c105-335">Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.</span><span class="sxs-lookup"><span data-stu-id="9c105-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9c105-336">Включить подтверждение учетной записи после того, как у сайта есть пользователи</span><span class="sxs-lookup"><span data-stu-id="9c105-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9c105-337">Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="9c105-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9c105-338">Существующие пользователи заблокированы, так как их учетные записи не подтверждены.</span><span class="sxs-lookup"><span data-stu-id="9c105-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9c105-339">Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="9c105-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9c105-340">Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.</span><span class="sxs-lookup"><span data-stu-id="9c105-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9c105-341">Подтвердите существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="9c105-341">Confirm existing users.</span></span> <span data-ttu-id="9c105-342">Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="9c105-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
