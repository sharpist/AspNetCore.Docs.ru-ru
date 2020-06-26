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
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404656"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="61c8e-103">Подтверждение учетной записи и восстановление пароля в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61c8e-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="61c8e-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Понант](https://github.com/Ponant)и [Джо аудетте](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="61c8e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="61c8e-105">В этом руководстве показано, как создать приложение ASP.NET Core с подтверждением электронной почты и сбросом пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="61c8e-106">Это руководство **не** является началом статьи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="61c8e-107">Вы должны быть знакомы с:</span><span class="sxs-lookup"><span data-stu-id="61c8e-107">You should be familiar with:</span></span>

* [<span data-ttu-id="61c8e-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61c8e-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="61c8e-109">Authentication</span><span class="sxs-lookup"><span data-stu-id="61c8e-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="61c8e-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="61c8e-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="61c8e-111">См. [этот PDF-файл](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) для ASP.NET Core версии 1,1.</span><span class="sxs-lookup"><span data-stu-id="61c8e-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="61c8e-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="61c8e-112">Prerequisites</span></span>

[<span data-ttu-id="61c8e-113">Пакет SDK для .NET Core 3.0 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="61c8e-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="61c8e-114">Создание и тестирование веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="61c8e-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="61c8e-115">Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="61c8e-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="61c8e-116">Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="61c8e-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="61c8e-117">После регистрации вы будете перенаправлены на `/Identity/Account/RegisterConfirmation` страницу, содержащую ссылку для имитации подтверждения по электронной почте:</span><span class="sxs-lookup"><span data-stu-id="61c8e-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="61c8e-118">Выберите `Click here to confirm your account` ссылку.</span><span class="sxs-lookup"><span data-stu-id="61c8e-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="61c8e-119">Выберите ссылку для **входа** и выполните вход с теми же учетными данными.</span><span class="sxs-lookup"><span data-stu-id="61c8e-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="61c8e-120">Выберите `Hello YourEmail@provider.com!` ссылку, которая перенаправит вас на `/Identity/Account/Manage/PersonalData` страницу.</span><span class="sxs-lookup"><span data-stu-id="61c8e-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="61c8e-121">Выберите вкладку **личные данные** слева, а затем щелкните **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="61c8e-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="61c8e-122">Настройка поставщика электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-122">Configure an email provider</span></span>

<span data-ttu-id="61c8e-123">В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="61c8e-124">Для отправки электронной почты требуется учетная запись SendGrid и ключ.</span><span class="sxs-lookup"><span data-stu-id="61c8e-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="61c8e-125">Вы можете использовать другие поставщики электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-125">You can use other email providers.</span></span> <span data-ttu-id="61c8e-126">Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу.</span><span class="sxs-lookup"><span data-stu-id="61c8e-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="61c8e-127">Протокол SMTP трудно защитить и настроить правильно.</span><span class="sxs-lookup"><span data-stu-id="61c8e-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="61c8e-128">Для учетной записи SendGrid может потребоваться [Добавить отправителя](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="61c8e-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="61c8e-129">Создайте класс для выборки ключа защищенной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="61c8e-130">Для этого примера создайте *Services/аусмессажесендероптионс. CS*:</span><span class="sxs-lookup"><span data-stu-id="61c8e-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="61c8e-131">Настройка секретов пользователя SendGrid</span><span class="sxs-lookup"><span data-stu-id="61c8e-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="61c8e-132">Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="61c8e-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="61c8e-133">Пример.</span><span class="sxs-lookup"><span data-stu-id="61c8e-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="61c8e-134">В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.</span><span class="sxs-lookup"><span data-stu-id="61c8e-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="61c8e-135">Содержимое *secrets.js* файла не шифруется.</span><span class="sxs-lookup"><span data-stu-id="61c8e-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="61c8e-136">В следующей разметке показано *secrets.js* файла.</span><span class="sxs-lookup"><span data-stu-id="61c8e-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="61c8e-137">`SendGridKey`Значение было удалено.</span><span class="sxs-lookup"><span data-stu-id="61c8e-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="61c8e-138">Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.</span><span class="sxs-lookup"><span data-stu-id="61c8e-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="61c8e-139">Установка SendGrid</span><span class="sxs-lookup"><span data-stu-id="61c8e-139">Install SendGrid</span></span>

<span data-ttu-id="61c8e-140">В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.</span><span class="sxs-lookup"><span data-stu-id="61c8e-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="61c8e-141">Установите `SendGrid` пакет NuGet:</span><span class="sxs-lookup"><span data-stu-id="61c8e-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c8e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c8e-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="61c8e-143">В консоли диспетчера пакетов введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="61c8e-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="61c8e-144">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="61c8e-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="61c8e-145">В консоли введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="61c8e-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="61c8e-146">Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.</span><span class="sxs-lookup"><span data-stu-id="61c8e-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="61c8e-147">Реализация Иемаилсендер</span><span class="sxs-lookup"><span data-stu-id="61c8e-147">Implement IEmailSender</span></span>

<span data-ttu-id="61c8e-148">Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:</span><span class="sxs-lookup"><span data-stu-id="61c8e-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="61c8e-149">Настройка запуска для поддержки электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-149">Configure startup to support email</span></span>

<span data-ttu-id="61c8e-150">Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="61c8e-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="61c8e-151">Добавьте `EmailSender` в качестве временной службы.</span><span class="sxs-lookup"><span data-stu-id="61c8e-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="61c8e-152">Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.</span><span class="sxs-lookup"><span data-stu-id="61c8e-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="61c8e-153">Регистрация, подтверждение электронной почты и сброс пароля</span><span class="sxs-lookup"><span data-stu-id="61c8e-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="61c8e-154">Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="61c8e-155">Запуск приложения и регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="61c8e-155">Run the app and register a new user</span></span>
* <span data-ttu-id="61c8e-156">Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="61c8e-157">Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .</span><span class="sxs-lookup"><span data-stu-id="61c8e-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="61c8e-158">Щелкните ссылку, чтобы подтвердить свою электронную почту.</span><span class="sxs-lookup"><span data-stu-id="61c8e-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="61c8e-159">Выполните вход, используя свой адрес электронной почты и пароль.</span><span class="sxs-lookup"><span data-stu-id="61c8e-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="61c8e-160">Выполните выход.</span><span class="sxs-lookup"><span data-stu-id="61c8e-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="61c8e-161">Проверка сброса пароля</span><span class="sxs-lookup"><span data-stu-id="61c8e-161">Test password reset</span></span>

* <span data-ttu-id="61c8e-162">Если вы вошли в этот компьютер, выберите **выход**.</span><span class="sxs-lookup"><span data-stu-id="61c8e-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="61c8e-163">Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .</span><span class="sxs-lookup"><span data-stu-id="61c8e-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="61c8e-164">Введите адрес электронной почты, использованный для регистрации учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="61c8e-165">Отправляется сообщение электронной почты со ссылкой для сброса пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="61c8e-166">Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль.</span><span class="sxs-lookup"><span data-stu-id="61c8e-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="61c8e-167">После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="61c8e-168">Изменить время ожидания для электронной почты и активности</span><span class="sxs-lookup"><span data-stu-id="61c8e-168">Change email and activity timeout</span></span>

<span data-ttu-id="61c8e-169">Время ожидания бездействия по умолчанию составляет 14 дней.</span><span class="sxs-lookup"><span data-stu-id="61c8e-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="61c8e-170">Следующий код задает время ожидания простоя в 5 дней:</span><span class="sxs-lookup"><span data-stu-id="61c8e-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="61c8e-171">Изменить все продолжительность существования маркеров защиты данных</span><span class="sxs-lookup"><span data-stu-id="61c8e-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="61c8e-172">Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:</span><span class="sxs-lookup"><span data-stu-id="61c8e-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="61c8e-173">Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="61c8e-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="61c8e-174">Изменение срока существования маркера электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-174">Change the email token lifespan</span></span>

<span data-ttu-id="61c8e-175">Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="61c8e-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="61c8e-176">В этом разделе показано, как изменить срок существования маркера электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="61c8e-177">Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="61c8e-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="61c8e-178">Добавьте настраиваемый поставщик в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="61c8e-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="61c8e-179">Подтверждение повторной отправки электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-179">Resend email confirmation</span></span>

<span data-ttu-id="61c8e-180">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="61c8e-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="61c8e-181">Отладка электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-181">Debug email</span></span>

<span data-ttu-id="61c8e-182">Если вы не можете работать с электронной почтой:</span><span class="sxs-lookup"><span data-stu-id="61c8e-182">If you can't get email working:</span></span>

* <span data-ttu-id="61c8e-183">Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="61c8e-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="61c8e-184">Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="61c8e-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="61c8e-185">Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="61c8e-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="61c8e-186">Проверьте папку спама.</span><span class="sxs-lookup"><span data-stu-id="61c8e-186">Check your spam folder.</span></span>
* <span data-ttu-id="61c8e-187">Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).</span><span class="sxs-lookup"><span data-stu-id="61c8e-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="61c8e-188">Попробуйте отправить их в другую учетную запись электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="61c8e-189">В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке.</span><span class="sxs-lookup"><span data-stu-id="61c8e-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="61c8e-190">Если приложение публикуется в Azure, задайте секреты SendGrid как параметры приложения на портале веб-приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="61c8e-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="61c8e-191">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="61c8e-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="61c8e-192">Объединение социальных и местных учетных записей входа</span><span class="sxs-lookup"><span data-stu-id="61c8e-192">Combine social and local login accounts</span></span>

<span data-ttu-id="61c8e-193">Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61c8e-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="61c8e-194">См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="61c8e-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="61c8e-195">Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту.</span><span class="sxs-lookup"><span data-stu-id="61c8e-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="61c8e-196">В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.</span><span class="sxs-lookup"><span data-stu-id="61c8e-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

<span data-ttu-id="61c8e-198">Щелкните ссылку **Управление** .</span><span class="sxs-lookup"><span data-stu-id="61c8e-198">Click on the **Manage** link.</span></span> <span data-ttu-id="61c8e-199">Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.</span><span class="sxs-lookup"><span data-stu-id="61c8e-199">Note the 0 external (social logins) associated with this account.</span></span>

![Управление представлением](accconfirm/_static/manage.png)

<span data-ttu-id="61c8e-201">Щелкните ссылку на другую службу входа и примите запросы приложения.</span><span class="sxs-lookup"><span data-stu-id="61c8e-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="61c8e-202">На следующем рисунке Facebook является внешним поставщиком проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="61c8e-202">In the following image, Facebook is the external authentication provider:</span></span>

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="61c8e-204">Две учетные записи были объединены.</span><span class="sxs-lookup"><span data-stu-id="61c8e-204">The two accounts have been combined.</span></span> <span data-ttu-id="61c8e-205">Вы можете войти с помощью любой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-205">You are able to sign in with either account.</span></span> <span data-ttu-id="61c8e-206">Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="61c8e-207">Включить подтверждение учетной записи после того, как у сайта есть пользователи</span><span class="sxs-lookup"><span data-stu-id="61c8e-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="61c8e-208">Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="61c8e-209">Существующие пользователи заблокированы, так как их учетные записи не подтверждены.</span><span class="sxs-lookup"><span data-stu-id="61c8e-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="61c8e-210">Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="61c8e-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="61c8e-211">Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.</span><span class="sxs-lookup"><span data-stu-id="61c8e-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="61c8e-212">Подтвердите существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-212">Confirm existing users.</span></span> <span data-ttu-id="61c8e-213">Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="61c8e-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="61c8e-214">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="61c8e-214">Prerequisites</span></span>

[<span data-ttu-id="61c8e-215">Пакет SDK для .NET Core 2,2 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="61c8e-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="61c8e-216">Создание веб-приложения и шаблонаIdentity</span><span class="sxs-lookup"><span data-stu-id="61c8e-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="61c8e-217">Выполните следующие команды, чтобы создать веб-приложение с проверкой подлинности.</span><span class="sxs-lookup"><span data-stu-id="61c8e-217">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="61c8e-218">Тестирование регистрации нового пользователя</span><span class="sxs-lookup"><span data-stu-id="61c8e-218">Test new user registration</span></span>

<span data-ttu-id="61c8e-219">Запустите приложение, выберите ссылку **Register** и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="61c8e-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="61c8e-220">На этом этапе единственной проверкой адреса электронной почты является [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="61c8e-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="61c8e-221">После отправки регистрации вы вошли в приложение.</span><span class="sxs-lookup"><span data-stu-id="61c8e-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="61c8e-222">Далее в этом руководстве код обновляется, поэтому новые пользователи не смогут войти в систему, пока не будет проверена электронная почта.</span><span class="sxs-lookup"><span data-stu-id="61c8e-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="61c8e-223">Обратите внимание, что `EmailConfirmed` поле таблицы имеет значение `False` .</span><span class="sxs-lookup"><span data-stu-id="61c8e-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="61c8e-224">Вы можете снова использовать это сообщение на следующем шаге, когда приложение отправит сообщение электронной почты с подтверждением.</span><span class="sxs-lookup"><span data-stu-id="61c8e-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="61c8e-225">Щелкните строку правой кнопкой мыши и выберите команду **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="61c8e-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="61c8e-226">Удаление псевдонима электронной почты упрощает следующие шаги.</span><span class="sxs-lookup"><span data-stu-id="61c8e-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="61c8e-227">Требовать подтверждение по электронной почте</span><span class="sxs-lookup"><span data-stu-id="61c8e-227">Require email confirmation</span></span>

<span data-ttu-id="61c8e-228">Рекомендуется подтвердить сообщение электронной почты о новой регистрации пользователя.</span><span class="sxs-lookup"><span data-stu-id="61c8e-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="61c8e-229">Подтверждение по электронной почте помогает проверить, что они не олицетворяют другого пользователя (т. е. они не зарегистрированы в сообщении электронной почты другого пользователя).</span><span class="sxs-lookup"><span data-stu-id="61c8e-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="61c8e-230">Предположим, у вас есть дискуссионный форум, и вы хотите предотвратить yli@example.com регистрацию "" в виде "" nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="61c8e-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="61c8e-231">Без подтверждения по электронной почте " nolivetto@contoso.com " может получить от приложения нежелательное сообщение электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="61c8e-232">Предположим, что пользователь случайно зарегистрировался как " ylo@example.com " и не заметил опечатку "или".</span><span class="sxs-lookup"><span data-stu-id="61c8e-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="61c8e-233">Они не смогут использовать восстановление пароля, так как у приложения нет нужной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="61c8e-234">Подтверждение по электронной почте обеспечивает ограниченную защиту от программы-роботы.</span><span class="sxs-lookup"><span data-stu-id="61c8e-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="61c8e-235">Подтверждение по электронной почте не обеспечивает защиту от вредоносных пользователей со многими учетными записями электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="61c8e-236">Обычно требуется запретить новым пользователям отправлять данные на ваш веб-сайт, прежде чем они будут иметь подтвержденное сообщение электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="61c8e-237">Обновление `Startup.ConfigureServices` для запроса подтвержденного адреса электронной почты:</span><span class="sxs-lookup"><span data-stu-id="61c8e-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="61c8e-238">`config.SignIn.RequireConfirmedEmail = true;`запрещает зарегистрированным пользователям входить в систему, пока не будет подтверждено их электронная почта.</span><span class="sxs-lookup"><span data-stu-id="61c8e-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="61c8e-239">Настройка поставщика электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-239">Configure email provider</span></span>

<span data-ttu-id="61c8e-240">В этом руководстве [SendGrid](https://sendgrid.com) используется для отправки электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="61c8e-241">Для отправки электронной почты требуется учетная запись SendGrid и ключ.</span><span class="sxs-lookup"><span data-stu-id="61c8e-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="61c8e-242">Вы можете использовать другие поставщики электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-242">You can use other email providers.</span></span> <span data-ttu-id="61c8e-243">ASP.NET Core 2. x включает `System.Net.Mail` , что позволяет отправлять электронную почту из приложения.</span><span class="sxs-lookup"><span data-stu-id="61c8e-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="61c8e-244">Для отправки электронной почты рекомендуется использовать SendGrid или другую почтовую службу.</span><span class="sxs-lookup"><span data-stu-id="61c8e-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="61c8e-245">Протокол SMTP трудно защитить и настроить правильно.</span><span class="sxs-lookup"><span data-stu-id="61c8e-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="61c8e-246">Создайте класс для выборки ключа защищенной электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="61c8e-247">Для этого примера создайте *Services/аусмессажесендероптионс. CS*:</span><span class="sxs-lookup"><span data-stu-id="61c8e-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="61c8e-248">Настройка секретов пользователя SendGrid</span><span class="sxs-lookup"><span data-stu-id="61c8e-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="61c8e-249">Задайте `SendGridUser` и `SendGridKey` с помощью [средства Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="61c8e-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="61c8e-250">Пример.</span><span class="sxs-lookup"><span data-stu-id="61c8e-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="61c8e-251">В Windows Диспетчер секретов сохраняет пары "ключ-значение" в *secrets.jsдля* файла в `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` каталоге.</span><span class="sxs-lookup"><span data-stu-id="61c8e-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="61c8e-252">Содержимое *secrets.js* файла не шифруется.</span><span class="sxs-lookup"><span data-stu-id="61c8e-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="61c8e-253">В следующей разметке показано *secrets.js* файла.</span><span class="sxs-lookup"><span data-stu-id="61c8e-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="61c8e-254">`SendGridKey`Значение было удалено.</span><span class="sxs-lookup"><span data-stu-id="61c8e-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="61c8e-255">Дополнительные сведения см. в разделе [шаблон](xref:fundamentals/configuration/options) и [Конфигурация](xref:fundamentals/configuration/index)параметров.</span><span class="sxs-lookup"><span data-stu-id="61c8e-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="61c8e-256">Установка SendGrid</span><span class="sxs-lookup"><span data-stu-id="61c8e-256">Install SendGrid</span></span>

<span data-ttu-id="61c8e-257">В этом руководстве показано, как добавлять уведомления по электронной почте через [SendGrid](https://sendgrid.com/), но можно отправлять сообщения электронной почты с помощью SMTP и других механизмов.</span><span class="sxs-lookup"><span data-stu-id="61c8e-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="61c8e-258">Установите `SendGrid` пакет NuGet:</span><span class="sxs-lookup"><span data-stu-id="61c8e-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c8e-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c8e-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="61c8e-260">В консоли диспетчера пакетов введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="61c8e-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="61c8e-261">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="61c8e-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="61c8e-262">В консоли введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="61c8e-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="61c8e-263">Ознакомьтесь [со статьей начало работы с SendGrid](https://sendgrid.com/free/) , чтобы зарегистрироваться для получения бесплатной учетной записи SendGrid.</span><span class="sxs-lookup"><span data-stu-id="61c8e-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="61c8e-264">Реализация Иемаилсендер</span><span class="sxs-lookup"><span data-stu-id="61c8e-264">Implement IEmailSender</span></span>

<span data-ttu-id="61c8e-265">Чтобы реализовать `IEmailSender` , создайте *службы/емаилсендер. CS* с кодом, аналогичным следующему:</span><span class="sxs-lookup"><span data-stu-id="61c8e-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="61c8e-266">Настройка запуска для поддержки электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-266">Configure startup to support email</span></span>

<span data-ttu-id="61c8e-267">Добавьте следующий код в `ConfigureServices` метод в файле *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="61c8e-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="61c8e-268">Добавьте `EmailSender` в качестве временной службы.</span><span class="sxs-lookup"><span data-stu-id="61c8e-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="61c8e-269">Зарегистрируйте `AuthMessageSenderOptions` экземпляр конфигурации.</span><span class="sxs-lookup"><span data-stu-id="61c8e-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="61c8e-270">Включение подтверждения учетной записи и восстановление пароля</span><span class="sxs-lookup"><span data-stu-id="61c8e-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="61c8e-271">Шаблон содержит код для подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="61c8e-272">Найдите `OnPostAsync` метод в *области (Areas/ Identity /Пажес/аккаунт/регистер.кштмл.КС*).</span><span class="sxs-lookup"><span data-stu-id="61c8e-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="61c8e-273">Запретите автоматический вход новых зарегистрированных пользователей с помощью комментария к следующей строке:</span><span class="sxs-lookup"><span data-stu-id="61c8e-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="61c8e-274">Показан полный метод с выделенной измененной строкой:</span><span class="sxs-lookup"><span data-stu-id="61c8e-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="61c8e-275">Регистрация, подтверждение электронной почты и сброс пароля</span><span class="sxs-lookup"><span data-stu-id="61c8e-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="61c8e-276">Запустите веб-приложение и протестируйте поток подтверждения учетной записи и восстановления пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="61c8e-277">Запуск приложения и регистрация нового пользователя</span><span class="sxs-lookup"><span data-stu-id="61c8e-277">Run the app and register a new user</span></span>
* <span data-ttu-id="61c8e-278">Проверьте свою электронную почту на наличие ссылки для подтверждения учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="61c8e-279">Если вы не получаете электронное письмо, см. раздел [Отладка электронной почты](#debug) .</span><span class="sxs-lookup"><span data-stu-id="61c8e-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="61c8e-280">Щелкните ссылку, чтобы подтвердить свою электронную почту.</span><span class="sxs-lookup"><span data-stu-id="61c8e-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="61c8e-281">Выполните вход, используя свой адрес электронной почты и пароль.</span><span class="sxs-lookup"><span data-stu-id="61c8e-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="61c8e-282">Выполните выход.</span><span class="sxs-lookup"><span data-stu-id="61c8e-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="61c8e-283">Просмотр страницы "Управление"</span><span class="sxs-lookup"><span data-stu-id="61c8e-283">View the manage page</span></span>

<span data-ttu-id="61c8e-284">Выберите имя пользователя в браузере: ![ окно браузера с именем пользователя.](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="61c8e-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="61c8e-285">Страница Управление отображается с выбранной вкладкой **профиль** .</span><span class="sxs-lookup"><span data-stu-id="61c8e-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="61c8e-286">В **сообщении электронной почты** отображается флажок, указывающий, что сообщение электронной почты подтверждено.</span><span class="sxs-lookup"><span data-stu-id="61c8e-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="61c8e-287">Проверка сброса пароля</span><span class="sxs-lookup"><span data-stu-id="61c8e-287">Test password reset</span></span>

* <span data-ttu-id="61c8e-288">Если вы вошли в этот компьютер, выберите **выход**.</span><span class="sxs-lookup"><span data-stu-id="61c8e-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="61c8e-289">Щелкните ссылку **войти** и выберите ссылку **забыли пароль?** .</span><span class="sxs-lookup"><span data-stu-id="61c8e-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="61c8e-290">Введите адрес электронной почты, использованный для регистрации учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="61c8e-291">Отправляется сообщение электронной почты со ссылкой для сброса пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="61c8e-292">Проверьте свой адрес электронной почты и щелкните ссылку, чтобы сбросить пароль.</span><span class="sxs-lookup"><span data-stu-id="61c8e-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="61c8e-293">После успешного сброса пароля можно выполнить вход с помощью электронной почты и нового пароля.</span><span class="sxs-lookup"><span data-stu-id="61c8e-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="61c8e-294">Изменить время ожидания для электронной почты и активности</span><span class="sxs-lookup"><span data-stu-id="61c8e-294">Change email and activity timeout</span></span>

<span data-ttu-id="61c8e-295">Время ожидания бездействия по умолчанию составляет 14 дней.</span><span class="sxs-lookup"><span data-stu-id="61c8e-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="61c8e-296">Следующий код задает время ожидания простоя в 5 дней:</span><span class="sxs-lookup"><span data-stu-id="61c8e-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="61c8e-297">Изменить все продолжительность существования маркеров защиты данных</span><span class="sxs-lookup"><span data-stu-id="61c8e-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="61c8e-298">Следующий код изменяет все маркеры защиты данных в течение времени ожидания до 3 часов:</span><span class="sxs-lookup"><span data-stu-id="61c8e-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="61c8e-299">Встроенные Identity маркеры пользователей (см. [AspNetCore/src/ Identity /екстенсионс.коре/СРК/токеноптионс.КС](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) имеют [время ожидания в один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="61c8e-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="61c8e-300">Изменение срока существования маркера электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-300">Change the email token lifespan</span></span>

<span data-ttu-id="61c8e-301">Срок существования токена [ Identity пользователя](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) по умолчанию — [один день](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="61c8e-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="61c8e-302">В этом разделе показано, как изменить срок существования маркера электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="61c8e-303">Добавьте пользовательские [датапротектортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) и <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="61c8e-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="61c8e-304">Добавьте настраиваемый поставщик в контейнер службы:</span><span class="sxs-lookup"><span data-stu-id="61c8e-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="61c8e-305">Подтверждение повторной отправки электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-305">Resend email confirmation</span></span>

<span data-ttu-id="61c8e-306">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="61c8e-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="61c8e-307">Отладка электронной почты</span><span class="sxs-lookup"><span data-stu-id="61c8e-307">Debug email</span></span>

<span data-ttu-id="61c8e-308">Если вы не можете работать с электронной почтой:</span><span class="sxs-lookup"><span data-stu-id="61c8e-308">If you can't get email working:</span></span>

* <span data-ttu-id="61c8e-309">Установите точку останова в `EmailSender.Execute` для проверки `SendGridClient.SendEmailAsync` .</span><span class="sxs-lookup"><span data-stu-id="61c8e-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="61c8e-310">Создайте [консольное приложение для отправки электронной почты](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) с помощью аналогичного кода в `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="61c8e-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="61c8e-311">Ознакомьтесь со страницей [действие по электронной почте](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="61c8e-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="61c8e-312">Проверьте папку спама.</span><span class="sxs-lookup"><span data-stu-id="61c8e-312">Check your spam folder.</span></span>
* <span data-ttu-id="61c8e-313">Попробуйте использовать другой псевдоним электронной почты в другом поставщике электронной почты (Microsoft, Yahoo, Gmail и т. д.).</span><span class="sxs-lookup"><span data-stu-id="61c8e-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="61c8e-314">Попробуйте отправить их в другую учетную запись электронной почты.</span><span class="sxs-lookup"><span data-stu-id="61c8e-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="61c8e-315">В целях **безопасности** рекомендуется **не** использовать рабочие секреты в тестировании и разработке.</span><span class="sxs-lookup"><span data-stu-id="61c8e-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="61c8e-316">При публикации приложения в Azure можно задать секреты SendGrid как параметры приложения на портале веб-приложения Azure.</span><span class="sxs-lookup"><span data-stu-id="61c8e-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="61c8e-317">Система конфигурации настроена на чтение ключей из переменных среды.</span><span class="sxs-lookup"><span data-stu-id="61c8e-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="61c8e-318">Объединение социальных и местных учетных записей входа</span><span class="sxs-lookup"><span data-stu-id="61c8e-318">Combine social and local login accounts</span></span>

<span data-ttu-id="61c8e-319">Для выполнения этого раздела необходимо сначала включить внешний поставщик проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61c8e-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="61c8e-320">См. статью [Проверка подлинности в Facebook, Google и внешнем поставщике](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="61c8e-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="61c8e-321">Вы можете объединить локальные и социальные учетные записи, щелкнув ссылку на электронную почту.</span><span class="sxs-lookup"><span data-stu-id="61c8e-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="61c8e-322">В следующей последовательности " RickAndMSFT@gmail.com " сначала создается как локальное имя входа, но вы можете сначала создать учетную запись в качестве имени для входа в социальных сетях, а затем добавить локальное имя входа.</span><span class="sxs-lookup"><span data-stu-id="61c8e-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Веб-приложение: RickAndMSFT@gmail.com Проверка подлинности пользователя](accconfirm/_static/rick.png)

<span data-ttu-id="61c8e-324">Щелкните ссылку **Управление** .</span><span class="sxs-lookup"><span data-stu-id="61c8e-324">Click on the **Manage** link.</span></span> <span data-ttu-id="61c8e-325">Обратите внимание на 0 внешних (социальных) имен, связанных с этой учетной записью.</span><span class="sxs-lookup"><span data-stu-id="61c8e-325">Note the 0 external (social logins) associated with this account.</span></span>

![Управление представлением](accconfirm/_static/manage.png)

<span data-ttu-id="61c8e-327">Щелкните ссылку на другую службу входа и примите запросы приложения.</span><span class="sxs-lookup"><span data-stu-id="61c8e-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="61c8e-328">На следующем рисунке Facebook является внешним поставщиком проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="61c8e-328">In the following image, Facebook is the external authentication provider:</span></span>

![Управление внешними именами входа Просмотр списка Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="61c8e-330">Две учетные записи были объединены.</span><span class="sxs-lookup"><span data-stu-id="61c8e-330">The two accounts have been combined.</span></span> <span data-ttu-id="61c8e-331">Вы можете войти с помощью любой учетной записи.</span><span class="sxs-lookup"><span data-stu-id="61c8e-331">You are able to sign in with either account.</span></span> <span data-ttu-id="61c8e-332">Пользователям может потребоваться добавить локальные учетные записи на случай, если служба проверки подлинности в социальных сетях не работает, или более вероятно, что они потеряют доступ к учетной записи социальных сетей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="61c8e-333">Включить подтверждение учетной записи после того, как у сайта есть пользователи</span><span class="sxs-lookup"><span data-stu-id="61c8e-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="61c8e-334">Включение подтверждения учетной записи на сайте с пользователями блокирует всех существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="61c8e-335">Существующие пользователи заблокированы, так как их учетные записи не подтверждены.</span><span class="sxs-lookup"><span data-stu-id="61c8e-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="61c8e-336">Чтобы обойти существующую блокировку пользователей, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="61c8e-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="61c8e-337">Обновите базу данных, чтобы пометить все существующие пользователи как подтвержденные.</span><span class="sxs-lookup"><span data-stu-id="61c8e-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="61c8e-338">Подтвердите существующих пользователей.</span><span class="sxs-lookup"><span data-stu-id="61c8e-338">Confirm existing users.</span></span> <span data-ttu-id="61c8e-339">Например, пакетная отправка сообщений электронной почты с ссылками для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="61c8e-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
