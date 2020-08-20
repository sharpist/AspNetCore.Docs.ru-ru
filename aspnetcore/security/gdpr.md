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
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Поддержка ЕС Общий регламент по защите данных (GDPR) в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core предоставляет интерфейсы API и шаблоны, помогающие удовлетворить некоторые из требований [ес общий регламент по защите данных (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на политику конфиденциальности и cookie использования.
* Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта.

Чтобы включить функцию согласия по умолчанию cookie , которая находится в шаблонах ASP.NET Core 2,2 в приложении ASP.NET Core 3,0, созданном шаблоном, выполните следующие действия.

* Добавьте `using Microsoft.AspNetCore.Http` в список директив using.
* Добавьте [ Cookie полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) в `Startup.ConfigureServices` [ Cookie политику и используйте](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ее для `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Добавьте cookie разрешение partial в файл *_layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Добавьте в проект файл * \_ Cookie консентпартиал. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Выберите версию ASP.NET Core 2,2 в этой статье, чтобы ознакомиться с cookie функцией согласия.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Шаблоны проектов включают точки расширения и разметку создана, которые можно заменить на политику конфиденциальности и cookie использования.
* cookieФункция согласия позволяет запрашивать (и контролировать) согласие от пользователей на хранение персональных данных. Если пользователь не предоставил разрешения на сбор данных, а в приложении задано [чеккконсентнидед](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) `true` , неважность cookie не отправляется в браузер.
* Cookieможно пометить как важный. Важность cookie s отправляется браузеру даже в том случае, если пользователь не был отключен и не отслеживается.
* [TempData и сеансы cookie не работают,](#tempdata) если отслеживание отключено.
* Страница [ Identity Управление](#pd) содержит ссылку для скачивания и удаления данных пользователя.

[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) позволяет тестировать большинство точек расширения GDPR и API, добавленных в шаблоны ASP.NET Core 2,1. Инструкции по тестированию см. в файле [сведений](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core поддержка GDPR в коде, созданном шаблоном

Razor Страницы и проекты MVC, созданные с помощью шаблонов проектов, включают следующую поддержку GDPR:

* [ Cookie Политики](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) и use задаются в `Startup` классе.
* [Частичное представление](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) * \_ Cookie консентпартиал. cshtml* . В этот файл входит кнопка **принять** . Когда пользователь нажимает кнопку " **принять** ", предоставляется согласие на доступ к магазину cookie .
* Страница Pages */privacy. cshtml* или views */Home/privacy. cshtml* предоставляет страницу для детализации политики конфиденциальности сайта. Файл * \_ Cookie консентпартиал. cshtml* создает ссылку на страницу «конфиденциальность».
* Для приложений, созданных с учетными записями отдельных пользователей, страница Управление предоставляет ссылки для скачивания и удаления [персональных данных пользователя](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookieПолициоптионс и использование Cookie политики

[ Cookie Полициоптионс](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) инициализируются в `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Используйте Cookie Политика](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) вызывается в `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieЧастичное представление Консентпартиал. cshtml

Частичное представление * \_ Cookie консентпартиал. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Это частичное:

* Получает состояние отслеживания для пользователя. Если приложение настроено для обязательного согласия, пользователь должен согласиться перед тем, как cookie можно будет относиться к нему. Если требуется согласие, cookie панель согласия зафиксирована в верхней части панели навигации, созданной файлом * \_ Layout. cshtml* .
* Предоставляет элемент HTML `<p>` для суммирования политики конфиденциальности и cookie использования.
* Содержит ссылку на страницу конфиденциальности или представление, где можно подробно описать политику конфиденциальности сайта.

## <a name="essential-no-loccookies"></a>Нужные cookie s

Если согласие на хранение cookie не было предоставлено, cookie в браузер отправляются только отмеченные ключевые элементы. Следующий код делает cookie важный:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>Поставщик TempData и состояние сеанса cookie не являются обязательными.

[Поставщик TempData](xref:fundamentals/app-state#tempdata) cookie не является обязательным. Если отслеживание отключено, то поставщик TempData не работает. Чтобы включить поставщик TempData при отключении отслеживания, пометьте TempData cookie как важный в `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Состояние сеанса](xref:fundamentals/app-state) cookie не являются обязательными. Состояние сеанса не работает, если отслеживание отключено. В следующем коде делаются все необходимое для сеанса cookie :

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Персональные данные

ASP.NET Core приложения, созданные с учетными записями отдельных пользователей, включают код для загрузки и удаления персональных данных.

Выберите имя пользователя, а затем выберите **личные данные**:

![Страница «Управление личными данными»](gdpr/_static/pd.png)

Примечания.

* Сведения о создании `Account/Manage` кода см. в разделе [формирование Identity шаблонов ](xref:security/authentication/scaffold-identity).
* Ссылки **Delete** и **download** действуют только для данных удостоверений по умолчанию. Приложения, которые создают настраиваемые пользовательские данные, должны быть расширены для удаления и скачивания пользовательских данных. Дополнительные сведения см. [в разделе Добавление, скачивание и удаление настраиваемых данных пользователя Identity в ](xref:security/authentication/add-user-data).
* Сохраненные токены для пользователя, хранящегося в Identity таблице базы данных `AspNetUserTokens` , удаляются при удалении пользователя через каскадное поведение удаления из-за [внешнего ключа](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Проверка подлинности внешнего поставщика](xref:security/authentication/social/index), например Facebook и Google, недоступна, пока cookie политика не будет принята.

::: moniker-end

## <a name="encryption-at-rest"></a>Шифрование при хранении

Некоторые базы данных и механизмы хранилища позволяют шифровать неактивных. Шифрование при хранении:

* Автоматически шифрует сохраненные данные.
* Шифруется без настройки, программирования или другой работы для программного обеспечения, обращающегося к данным.
* — Самый простой и надежный вариант.
* Позволяет базе данных управлять ключами и шифрованием.

Пример:

* Microsoft SQL и Azure SQL предоставляют [прозрачное шифрование данных](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [По умолчанию SQL Azure шифрует базу данных](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Большие двоичные объекты Azure, файлы, таблицы и хранилища очередей шифруются по умолчанию](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Для баз данных, которые не предоставляют встроенное шифрование, вы можете использовать шифрование дисков для обеспечения такой же защиты. Пример:

* [BitLocker для Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [екриптфс](https://launchpad.net/ecryptfs)
  * [Енкфс](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR — Добавление кнопки "отозвать согласие" в ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
