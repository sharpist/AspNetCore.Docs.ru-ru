---
title: Настройка внешнего входа учетной записи Майкрософт с ASP.NET Core
author: rick-anderson
description: В этом примере демонстрируется интеграция учетная запись Майкрософт проверки подлинности пользователей в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3430d842b6a4f7da30370977f72e6f132e28bb7f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634258"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Настройка внешнего входа учетной записи Майкрософт с ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом примере показано, как разрешить пользователям выполнять вход с помощью рабочей, учебной или личной учетная запись Майкрософт с помощью проекта ASP.NET Core 3,0, созданного на [предыдущей странице](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Создание приложения на портале разработчика Майкрософт

* Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) .
* Перейдите на страницу [портал Azure-регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) и создайте или войдите в учетная запись Майкрософт:

Если у вас нет учетная запись Майкрософт, выберите **создать**. После входа вы будете перенаправлены на **Регистрация приложений** страницу:

* Выбрать **новую регистрацию**
* Введите **Имя**.
* Выберите вариант для **поддерживаемых типов учетных записей**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* В разделе **URI перенаправления**введите URL-адрес разработки с `/signin-microsoft` добавлением. Например, `https://localhost:5001/signin-microsoft`. Схема проверки подлинности Майкрософт, настроенная далее в этом примере, автоматически обрабатывает запросы по `/signin-microsoft` маршруту для реализации потока OAuth.
* Нажмите кнопку **Зарегистрировать**.

### <a name="create-client-secret"></a>Создать секрет клиента

* В области слева щелкните **Сертификаты и секреты**.
* В разделе **секреты клиента**выберите **новый секрет клиента** .

  * Добавьте описание секрета клиента.
  * Нажмите кнопку **Добавить**.

* В разделе **секреты клиента**скопируйте значение секрета клиента.

Сегмент URI `/signin-microsoft` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Майкрософт. URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Майкрософт с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [микрософтаккаунтоптионс](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Хранение идентификатора и секрета клиента Майкрософт

Храните конфиденциальные параметры, такие как идентификатор клиента (Майкрософт) и секретные значения, с помощью [диспетчера секретов](xref:security/app-secrets). Для этого примера выполните следующие действия.

1. Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).
1. Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Microsoft:ClientId` и `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Настройка проверки подлинности учетной записи Майкрософт

Добавьте службу учетных записей Майкрософт в `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности учетной записи Майкрософт, см. в справочнике по API [микрософтаккаунтоптионс](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Это можно использовать для запроса различных сведений о пользователе.

## <a name="sign-in-with-microsoft-account"></a>Учетная запись Войдите с помощью учетной записи Майкрософт

Запустите приложение и нажмите кнопку **войти**. Появится возможность войти в систему с помощью Microsoft. Если щелкнуть Майкрософт, вы будете перенаправлены в корпорацию Майкрософт для проверки подлинности. После входа с помощью учетной записи Майкрософт вам будет предложено предоставить приложению доступ к вашим сведениям:

Коснитесь кнопки **Да** , и вы будете перенаправлены на веб-сайт, на котором можно задать свой адрес электронной почты.

Теперь вы выполнили вход с использованием учетных данных Майкрософт:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Устранение неполадок

* Если поставщик учетных записей Майкрософт перенаправит вас на страницу ошибки входа, обратите внимание на заголовок ошибки и описание параметры строки запроса непосредственно после `#` (хэш-кода) в универсальном коде ресурса (URI).

  Несмотря на то, что сообщение об ошибке указывает на проблему с проверкой подлинности Майкрософт, наиболее распространенной причиной является URI приложения, не совпадающий ни с одним из **URI перенаправления** , указанных для **веб-** платформы.
* Если Identity параметр не настроен с помощью вызова `services.AddIdentity` в `ConfigureServices` , попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"*. Шаблон проекта, используемый в этом образце, гарантирует, что это будет сделано.
* Если база данных сайта не была создана с применением первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* . Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* В этой статье показано, как можно пройти проверку подлинности в Майкрософт. Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).

* После публикации веб-сайта в веб-приложение Azure создайте секретные данные клиента на портале разработчика Майкрософт.

* Задайте `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` Параметры приложения и в портал Azure. Система конфигурации настроена на чтение ключей из переменных среды.
