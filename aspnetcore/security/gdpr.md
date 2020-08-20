---
title: Поддержка Общий регламент по защите данных (GDPR) в ASP.NET Core
author: rick-anderson
description: Узнайте, как получить доступ к точкам расширения GDPR в веб-приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: 35a12cb8d2a9617e51d886e798cff5ee60b0a8ad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634713"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="177c0-103">Поддержка ЕС Общий регламент по защите данных (GDPR) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="177c0-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="177c0-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="177c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="177c0-105">ASP.NET Core предоставляет интерфейсы API и шаблоны, помогающие удовлетворить некоторые из требований [ес общий регламент по защите данных (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="177c0-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="177c0-106">Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на политику конфиденциальности и cookie использования.</span><span class="sxs-lookup"><span data-stu-id="177c0-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="177c0-107">Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта.</span><span class="sxs-lookup"><span data-stu-id="177c0-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="177c0-108">Чтобы включить функцию согласия по умолчанию cookie , которая находится в шаблонах ASP.NET Core 2,2 в приложении ASP.NET Core 3,0, созданном шаблоном, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="177c0-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="177c0-109">Добавьте `using Microsoft.AspNetCore.Http` в список директив using.</span><span class="sxs-lookup"><span data-stu-id="177c0-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="177c0-110">Добавьте [ Cookie полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) в `Startup.ConfigureServices` [ Cookie политику и используйте](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ее для `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="177c0-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="177c0-111">Добавьте cookie разрешение partial в файл *_layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="177c0-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="177c0-112">Добавьте в проект файл \* \_ Cookie консентпартиал. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="177c0-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="177c0-113">Выберите версию ASP.NET Core 2,2 в этой статье, чтобы ознакомиться с cookie функцией согласия.</span><span class="sxs-lookup"><span data-stu-id="177c0-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="177c0-114">Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на политику конфиденциальности и cookie использования.</span><span class="sxs-lookup"><span data-stu-id="177c0-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="177c0-115">cookieФункция согласия позволяет запрашивать (и контролировать) согласие от пользователей на хранение персональных данных.</span><span class="sxs-lookup"><span data-stu-id="177c0-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="177c0-116">Если пользователь не предоставил разрешения на сбор данных, а в приложении задано [чеккконсентнидед](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) `true` , неважность cookie не отправляется в браузер.</span><span class="sxs-lookup"><span data-stu-id="177c0-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="177c0-117">Cookieможно пометить как важный.</span><span class="sxs-lookup"><span data-stu-id="177c0-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="177c0-118">Важность cookie s отправляется браузеру даже в том случае, если пользователь не был отключен и не отслеживается.</span><span class="sxs-lookup"><span data-stu-id="177c0-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="177c0-119">[TempData и сеансы cookie не работают,](#tempdata) если отслеживание отключено.</span><span class="sxs-lookup"><span data-stu-id="177c0-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="177c0-120">Страница [ Identity Управление](#pd) содержит ссылку для скачивания и удаления данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="177c0-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="177c0-121">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) позволяет тестировать большинство точек расширения GDPR и API, добавленных в шаблоны ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="177c0-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="177c0-122">Инструкции по тестированию см. в файле [сведений](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .</span><span class="sxs-lookup"><span data-stu-id="177c0-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="177c0-123">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="177c0-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="177c0-124">ASP.NET Core поддержка GDPR в коде, созданном шаблоном</span><span class="sxs-lookup"><span data-stu-id="177c0-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="177c0-125">Razor Страницы и проекты MVC, созданные с помощью шаблонов проектов, включают следующую поддержку GDPR:</span><span class="sxs-lookup"><span data-stu-id="177c0-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="177c0-126">[ Cookie Политики](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) и use задаются в `Startup` классе.</span><span class="sxs-lookup"><span data-stu-id="177c0-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="177c0-127">[Частичное представление](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) \* \_ Cookie консентпартиал. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="177c0-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="177c0-128">В этот файл входит кнопка **принять** .</span><span class="sxs-lookup"><span data-stu-id="177c0-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="177c0-129">Когда пользователь нажимает кнопку " **принять** ", предоставляется согласие на доступ к магазину cookie .</span><span class="sxs-lookup"><span data-stu-id="177c0-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="177c0-130">Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта.</span><span class="sxs-lookup"><span data-stu-id="177c0-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="177c0-131">Файл \* \_ Cookie консентпартиал. cshtml\* создает ссылку на страницу «конфиденциальность».</span><span class="sxs-lookup"><span data-stu-id="177c0-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="177c0-132">Для приложений, созданных с учетными записями отдельных пользователей, страница Управление предоставляет ссылки для скачивания и удаления [персональных данных пользователя](#pd).</span><span class="sxs-lookup"><span data-stu-id="177c0-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="177c0-133">CookieПолициоптионс и использование Cookie политики</span><span class="sxs-lookup"><span data-stu-id="177c0-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="177c0-134">[ Cookie Полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) инициализируются в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="177c0-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="177c0-135">[Используйте Cookie Политика](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) вызывается в `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="177c0-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="177c0-136">\_CookieЧастичное представление Консентпартиал. cshtml</span><span class="sxs-lookup"><span data-stu-id="177c0-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="177c0-137">Частичное представление \* \_ Cookie консентпартиал. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="177c0-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="177c0-138">Это частичное:</span><span class="sxs-lookup"><span data-stu-id="177c0-138">This partial:</span></span>

* <span data-ttu-id="177c0-139">Получает состояние отслеживания для пользователя.</span><span class="sxs-lookup"><span data-stu-id="177c0-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="177c0-140">Если приложение настроено для обязательного согласия, пользователь должен согласиться перед тем, как cookie можно будет относиться к нему.</span><span class="sxs-lookup"><span data-stu-id="177c0-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="177c0-141">Если требуется согласие, cookie панель согласия зафиксирована в верхней части панели навигации, созданной файлом \* \_ Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="177c0-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="177c0-142">Предоставляет элемент HTML `<p>` для суммирования политики конфиденциальности и cookie использования.</span><span class="sxs-lookup"><span data-stu-id="177c0-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="177c0-143">Содержит ссылку на страницу конфиденциальности или представление, где можно подробно описать политику конфиденциальности сайта.</span><span class="sxs-lookup"><span data-stu-id="177c0-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="177c0-144">Нужные cookie s</span><span class="sxs-lookup"><span data-stu-id="177c0-144">Essential cookies</span></span>

<span data-ttu-id="177c0-145">Если согласие на хранение cookie не было предоставлено, cookie в браузер отправляются только отмеченные ключевые элементы.</span><span class="sxs-lookup"><span data-stu-id="177c0-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="177c0-146">Следующий код делает cookie важный:</span><span class="sxs-lookup"><span data-stu-id="177c0-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="177c0-147">Поставщик TempData и состояние сеанса cookie не являются обязательными.</span><span class="sxs-lookup"><span data-stu-id="177c0-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="177c0-148">[Поставщик TempData](xref:fundamentals/app-state#tempdata) cookie не является обязательным.</span><span class="sxs-lookup"><span data-stu-id="177c0-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="177c0-149">Если отслеживание отключено, то поставщик TempData не работает.</span><span class="sxs-lookup"><span data-stu-id="177c0-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="177c0-150">Чтобы включить поставщик TempData при отключении отслеживания, пометьте TempData cookie как важный в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="177c0-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="177c0-151">[Состояние сеанса](xref:fundamentals/app-state) cookie не являются обязательными.</span><span class="sxs-lookup"><span data-stu-id="177c0-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="177c0-152">Состояние сеанса не работает, если отслеживание отключено.</span><span class="sxs-lookup"><span data-stu-id="177c0-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="177c0-153">В следующем коде делаются все необходимое для сеанса cookie :</span><span class="sxs-lookup"><span data-stu-id="177c0-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="177c0-154">Персональные данные</span><span class="sxs-lookup"><span data-stu-id="177c0-154">Personal data</span></span>

<span data-ttu-id="177c0-155">ASP.NET Core приложения, созданные с учетными записями отдельных пользователей, включают код для загрузки и удаления персональных данных.</span><span class="sxs-lookup"><span data-stu-id="177c0-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="177c0-156">Выберите имя пользователя, а затем выберите **личные данные**:</span><span class="sxs-lookup"><span data-stu-id="177c0-156">Select the user name and then select **Personal data**:</span></span>

![Страница «Управление личными данными»](gdpr/_static/pd.png)

<span data-ttu-id="177c0-158">Примечания.</span><span class="sxs-lookup"><span data-stu-id="177c0-158">Notes:</span></span>

* <span data-ttu-id="177c0-159">Сведения о создании `Account/Manage` кода см. в разделе [формирование Identity шаблонов ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="177c0-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="177c0-160">Ссылки **Delete** и **download** действуют только для данных удостоверений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="177c0-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="177c0-161">Приложения, которые создают настраиваемые пользовательские данные, должны быть расширены для удаления и скачивания пользовательских данных.</span><span class="sxs-lookup"><span data-stu-id="177c0-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="177c0-162">Дополнительные сведения см. [в разделе Добавление, скачивание и удаление настраиваемых данных пользователя Identity в ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="177c0-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="177c0-163">Сохраненные токены для пользователя, хранящегося в Identity таблице базы данных `AspNetUserTokens` , удаляются при удалении пользователя через каскадное поведение удаления из-за [внешнего ключа](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="177c0-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="177c0-164">[Проверка подлинности внешнего поставщика](xref:security/authentication/social/index), например Facebook и Google, недоступна, пока cookie политика не будет принята.</span><span class="sxs-lookup"><span data-stu-id="177c0-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="177c0-165">Шифрование при хранении</span><span class="sxs-lookup"><span data-stu-id="177c0-165">Encryption at rest</span></span>

<span data-ttu-id="177c0-166">Некоторые базы данных и механизмы хранилища позволяют шифровать неактивных.</span><span class="sxs-lookup"><span data-stu-id="177c0-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="177c0-167">Шифрование при хранении:</span><span class="sxs-lookup"><span data-stu-id="177c0-167">Encryption at rest:</span></span>

* <span data-ttu-id="177c0-168">Автоматически шифрует сохраненные данные.</span><span class="sxs-lookup"><span data-stu-id="177c0-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="177c0-169">Шифруется без настройки, программирования или другой работы для программного обеспечения, обращающегося к данным.</span><span class="sxs-lookup"><span data-stu-id="177c0-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="177c0-170">— Самый простой и надежный вариант.</span><span class="sxs-lookup"><span data-stu-id="177c0-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="177c0-171">Позволяет базе данных управлять ключами и шифрованием.</span><span class="sxs-lookup"><span data-stu-id="177c0-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="177c0-172">Пример:</span><span class="sxs-lookup"><span data-stu-id="177c0-172">For example:</span></span>

* <span data-ttu-id="177c0-173">Microsoft SQL и Azure SQL предоставляют [прозрачное шифрование данных](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="177c0-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="177c0-174">По умолчанию SQL Azure шифрует базу данных</span><span class="sxs-lookup"><span data-stu-id="177c0-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="177c0-175">[Большие двоичные объекты Azure, файлы, таблицы и хранилища очередей шифруются по умолчанию](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="177c0-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="177c0-176">Для баз данных, которые не предоставляют встроенное шифрование, вы можете использовать шифрование дисков для обеспечения такой же защиты.</span><span class="sxs-lookup"><span data-stu-id="177c0-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="177c0-177">Пример:</span><span class="sxs-lookup"><span data-stu-id="177c0-177">For example:</span></span>

* [<span data-ttu-id="177c0-178">BitLocker для Windows Server</span><span class="sxs-lookup"><span data-stu-id="177c0-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="177c0-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="177c0-179">Linux:</span></span>
  * [<span data-ttu-id="177c0-180">екриптфс</span><span class="sxs-lookup"><span data-stu-id="177c0-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="177c0-181">[Енкфс](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="177c0-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="177c0-182">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="177c0-182">Additional resources</span></span>

* [<span data-ttu-id="177c0-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="177c0-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="177c0-184">GDPR — Добавление кнопки "отозвать согласие" в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="177c0-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
