---
title: Настройка внешнего входа в Twitter с помощью ASP.NET Core
author: rick-anderson
description: В этом руководстве демонстрируется интеграция аутентификации пользователя учетной записи Twitter с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: mvc
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 61c983de33b91a16ad207d8a350daf4859c89eaf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406099"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Настройка внешнего входа в Twitter с помощью ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом примере показано, как разрешить пользователям [входить с помощью учетной записи Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) , используя пример проекта ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Создание приложения в Twitter

* Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) .

* Перейдите к [https://apps.twitter.com/](https://apps.twitter.com/) и войдите. Если у вас еще нет учетной записи Twitter, используйте ссылку **[зарегистрироваться сейчас](https://twitter.com/signup)** , чтобы создать ее.

* Выберите **создать приложение**. Укажите **имя приложения**, **Описание приложения** и универсальный код ресурса (URI) общедоступного **веб-сайта** (это может быть временным, пока не будет зарегистрировано доменное имя):

* Установите флажок **включить вход с помощью Twitter** .

* Microsoft. AspNetCore.Identity требует, чтобы по умолчанию у пользователей был адрес электронной почты. Перейдите на вкладку **разрешения** , нажмите кнопку " **изменить** " и установите флажок " **запросить адрес электронной почты у пользователей**".

* Введите URI разработки с `/signin-twitter` добавлением в поле **URL-адреса обратного вызова** (например, `https://webapp128.azurewebsites.net/signin-twitter` ). Схема проверки подлинности Twitter, настроенная далее в этом примере, автоматически обрабатывает запросы по `/signin-twitter` маршруту для реализации потока OAuth.

  > [!NOTE]
  > Сегмент URI `/signin-twitter` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Twitter. URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Twitter с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [твиттероптионс](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Заполните оставшуюся часть формы и выберите **создать**. Отобразятся сведения о новом приложении:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Хранение ключа и секрета API потребителя Twitter

Храните конфиденциальные параметры, такие как ключ API потребителя Twitter и секретный код, с помощью [диспетчера секретов](xref:security/app-secrets). Для этого примера выполните следующие действия.

1. Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).
1. Храните конфиденциальные параметры в локальном хранилище секретов с помощью ключей секретов `Authentication:Twitter:ConsumerKey` и `Authentication:Twitter:ConsumerSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Эти маркеры можно найти на вкладке **ключи и маркеры доступа** после создания нового приложения Twitter.

## <a name="configure-twitter-authentication"></a>Настройка проверки подлинности Twitter

Добавьте службу Twitter в `ConfigureServices` метод в файле *Startup.CS* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности в Twitter, см. в справочнике по API [твиттероптионс](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) . Это можно использовать для запроса различных сведений о пользователе.

## <a name="sign-in-with-twitter"></a>Вход с помощью Twitter

Запустите приложение и выберите **Вход**. Появится возможность входа с помощью Twitter:

При щелчке **Twitter** в Twitter перенаправления для проверки подлинности:

После ввода учетных данных Twitter вы будете перенаправлены на веб-сайт, на котором можно задать свой адрес электронной почты.

Теперь вы выполнили вход с использованием учетных данных Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Устранение неполадок

* **Только ASP.NET Core 2. x:** Если Identity параметр не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"*. Шаблон проекта, используемый в этом образце, гарантирует, что это будет сделано.
* Если база данных сайта не была создана с применением первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* . Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* В этой статье показано, как можно пройти проверку подлинности в Twitter. Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).

* После публикации веб-сайта в веб-приложение Azure необходимо сбросить настройки `ConsumerSecret` на портале разработчика Twitter.

* Задайте `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` Параметры приложения и в портал Azure. Система конфигурации настроена на чтение ключей из переменных среды.
