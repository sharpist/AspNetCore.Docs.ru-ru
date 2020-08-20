---
title: Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core
author: rick-anderson
description: Узнайте, как включить создание QR-кода для приложений TOTP Authenticator, работающих с ASP.NET Core двухфакторной проверки подлинности.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: e61aa925262fc9fe25c7bb2d37958cfaa308aeaf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630800"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="f06cc-103">Включение создания QR-кода для приложений TOTP Authenticator в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f06cc-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f06cc-104">Для QR-кодов требуется ASP.NET Core 2,0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="f06cc-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f06cc-105">ASP.NET Core поставляется с поддержкой приложений проверки подлинности для индивидуальной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="f06cc-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="f06cc-106">Приложения проверки подлинности с двумя факторами проверки подлинности (2FA), использующие алгоритм одноразового пароля (TOTP) на основе времени, являются рекомендуемым подходом к 2FA.</span><span class="sxs-lookup"><span data-stu-id="f06cc-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="f06cc-107">2FA с использованием TOTP является предпочтительным для SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="f06cc-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="f06cc-108">Приложение для проверки подлинности предоставляет код из 6 – 8 цифр, который пользователи должны ввести после подтверждения имени пользователя и пароля.</span><span class="sxs-lookup"><span data-stu-id="f06cc-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="f06cc-109">Обычно приложение для проверки подлинности устанавливается на смарт-телефон.</span><span class="sxs-lookup"><span data-stu-id="f06cc-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="f06cc-110">Шаблоны веб-приложений ASP.NET Core поддерживают средства проверки подлинности, но не обеспечивают поддержку создания Кркоде.</span><span class="sxs-lookup"><span data-stu-id="f06cc-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="f06cc-111">Кркоденые генераторы упрощают настройку 2FA.</span><span class="sxs-lookup"><span data-stu-id="f06cc-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="f06cc-112">В этом документе рассматривается добавление QR- [кода](https://wikipedia.org/wiki/QR_code) на страницу настройки 2FA.</span><span class="sxs-lookup"><span data-stu-id="f06cc-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="f06cc-113">Двухфакторная проверка подлинности не выполняется с помощью внешнего поставщика проверки подлинности, например [Google](xref:security/authentication/google-logins) или [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="f06cc-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="f06cc-114">Внешние имена входа защищены любым механизмом, предоставляемым внешним поставщиком входа.</span><span class="sxs-lookup"><span data-stu-id="f06cc-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="f06cc-115">Рассмотрим, например, для поставщика проверки подлинности [Майкрософт](xref:security/authentication/microsoft-logins) требуется аппаратный ключ или другой подход 2FA.</span><span class="sxs-lookup"><span data-stu-id="f06cc-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="f06cc-116">Если шаблоны по умолчанию применяют "Local" 2FA, то пользователям потребуется выполнить два подхода 2FA, что не является часто используемым сценарием.</span><span class="sxs-lookup"><span data-stu-id="f06cc-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="f06cc-117">Добавление QR-кодов на страницу настройки 2FA</span><span class="sxs-lookup"><span data-stu-id="f06cc-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="f06cc-118">В этих инструкциях используется *qrcode.js* из https://davidshimjs.github.io/qrcodejs/ репозитория.</span><span class="sxs-lookup"><span data-stu-id="f06cc-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="f06cc-119">Скачайте [ библиотекуqrcode.js JavaScript](https://davidshimjs.github.io/qrcodejs/) в `wwwroot\lib` папку проекта.</span><span class="sxs-lookup"><span data-stu-id="f06cc-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="f06cc-120">Следуйте инструкциям в разделе [формирование Identity шаблонов](xref:security/authentication/scaffold-identity) , чтобы создать */Ареас/ Identity /Пажес/аккаунт/манаже/енаблеаусентикатор.кштмл*.</span><span class="sxs-lookup"><span data-stu-id="f06cc-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="f06cc-121">В */Ареас/ Identity /Пажес/аккаунт/манаже/енаблеаусентикатор.кштмл*выберите раздел в `Scripts` конце файла:</span><span class="sxs-lookup"><span data-stu-id="f06cc-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="f06cc-122">В окне *страницы/учетная запись/управление/енаблеаусентикатор. cshtml* ( Razor страницы) или *views/Manage/енаблеаусентикатор. cshtml* (MVC) выберите `Scripts` раздел в конце файла:</span><span class="sxs-lookup"><span data-stu-id="f06cc-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="f06cc-123">Обновите `Scripts` раздел, чтобы добавить ссылку на `qrcodejs` добавленную библиотеку, и вызов для создания QR-кода.</span><span class="sxs-lookup"><span data-stu-id="f06cc-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="f06cc-124">Он должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f06cc-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="f06cc-125">Удалите абзац со ссылкой на эти инструкции.</span><span class="sxs-lookup"><span data-stu-id="f06cc-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="f06cc-126">Запустите приложение и убедитесь, что вы можете просмотреть QR-код и проверить код, который проверяется средством проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="f06cc-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="f06cc-127">Изменение имени сайта в QR-коде</span><span class="sxs-lookup"><span data-stu-id="f06cc-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f06cc-128">Имя сайта в QR-коде берется из имени проекта, выбранного при первоначальном создании проекта.</span><span class="sxs-lookup"><span data-stu-id="f06cc-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f06cc-129">Его можно изменить, выполнив поиск `GenerateQrCodeUri(string email, string unformattedKey)` метода в */Ареас/ Identity /Пажес/аккаунт/манаже/енаблеаусентикатор.кштмл.КС*.</span><span class="sxs-lookup"><span data-stu-id="f06cc-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f06cc-130">Имя сайта в QR-коде берется из имени проекта, выбранного при первоначальном создании проекта.</span><span class="sxs-lookup"><span data-stu-id="f06cc-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f06cc-131">Его можно изменить, выполнив поиск `GenerateQrCodeUri(string email, string unformattedKey)` метода в файле *pages/Account/Manage/енаблеаусентикатор. cshtml. CS* ( Razor pages) или в файле *Controllers/манажеконтроллер. CS* (MVC).</span><span class="sxs-lookup"><span data-stu-id="f06cc-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f06cc-132">Код по умолчанию из шаблона выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="f06cc-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="f06cc-133">Вторым параметром в вызове `string.Format` является имя вашего веб-узла, взятое из имени решения.</span><span class="sxs-lookup"><span data-stu-id="f06cc-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="f06cc-134">Его можно изменить на любое значение, но оно всегда должно быть в кодировке URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="f06cc-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="f06cc-135">Использование другой библиотеки QR-кодов</span><span class="sxs-lookup"><span data-stu-id="f06cc-135">Using a different QR Code library</span></span>

<span data-ttu-id="f06cc-136">Библиотеку QR-кода можно заменить предпочтительной библиотекой.</span><span class="sxs-lookup"><span data-stu-id="f06cc-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="f06cc-137">HTML содержит элемент, `qrCode` в который можно поместить QR-код любым механизмом, предоставляемым библиотекой.</span><span class="sxs-lookup"><span data-stu-id="f06cc-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="f06cc-138">URL-адрес в правильно отформатированном коде QR доступен в:</span><span class="sxs-lookup"><span data-stu-id="f06cc-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="f06cc-139">`AuthenticatorUri` свойство модели.</span><span class="sxs-lookup"><span data-stu-id="f06cc-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="f06cc-140">`data-url` в `qrCodeData` элементе.</span><span class="sxs-lookup"><span data-stu-id="f06cc-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="f06cc-141">Отклонение времени клиента и сервера TOTP</span><span class="sxs-lookup"><span data-stu-id="f06cc-141">TOTP client and server time skew</span></span>

<span data-ttu-id="f06cc-142">TOTP (одноразовый пароль на основе времени) зависит от сервера и устройства проверки подлинности с точным временем.</span><span class="sxs-lookup"><span data-stu-id="f06cc-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="f06cc-143">Маркеры только последний раз в течение 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="f06cc-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="f06cc-144">При сбое имен входа TOTP 2FA убедитесь, что время сервера точное и желательно синхронизироваться с точной службой NTP.</span><span class="sxs-lookup"><span data-stu-id="f06cc-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
