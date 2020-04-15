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
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Внешняя настройка входа в систему учетной записи Майкрософт с ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом примере показано, как дать возможность пользователям войти в систему с их работой, школой или личной учетной записью Майкрософт с помощью проекта ASP.NET Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index)

## <a name="create-the-app-in-microsoft-developer-portal"></a>Создание приложения на портале разработчиков Майкрософт

* Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet.
* Перейдите на портал Azure - Страница [регистрации приложений](https://go.microsoft.com/fwlink/?linkid=2083908) и создайте или войдите в учетную запись Майкрософт:

Если у вас нет учетной записи Майкрософт, выберите **создать ее.** После входе в систему вы перенаправываетесь на страницу **регистрации приложений:**

* Выберите **новую регистрацию**
* Введите **имя**.
* Выберите опцию для **типов поддерживаемых учетных записей.**  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* Под **перенаправлением URI**введите URL-адрес разработки с `/signin-microsoft` придативаемым. Например, `https://localhost:5001/signin-microsoft`. Схема проверки подлинности Майкрософт, настроенная позднее в этом примере, будет автоматически обрабатывать запросы на `/signin-microsoft` маршруте для реализации потока OAuth.
* Выберите **Регистр**

### <a name="create-client-secret"></a>Создание секрета клиента

* В левой панели выберите **Сертификаты & секреты.**
* Под **секретами клиента**, выберите **Новый секрет клиента**

  * Добавьте описание секрета клиента.
  * Выберите кнопку **Добавить.**

* Под **секретами клиента,** скопировать стоимость клиента секрет.

Сегмент `/signin-microsoft` URI устанавливается как обратный вызов по умолчанию поставщика аутентификации Майкрософт. Вы можете изменить URI вызова по умолчанию при настройке промежуточного программного обеспечения Microsoft по середине с помощью унаследованного [свойства RemoteAuthentication. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Храните идентификатор клиента и секрет Microsoft

Храните чувствительные настройки, такие как идентификатор клиента Microsoft и секретные значения с [помощью Secret Manager.](xref:security/app-secrets) Для этого примера используйте следующие шаги:

1. Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).
1. Храните чувствительные настройки в местном секретном магазине с секретными ключами `Authentication:Microsoft:ClientId` и: `Authentication:Microsoft:ClientSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Настройка аутентификации учетной записи Майкрософт

Добавьте службу учетной записи Майкрософт в: `Startup.ConfigureServices`

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией учетной записи Майкрософт, см. [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Это может быть использовано для запроса различной информации о пользователе.

## <a name="sign-in-with-microsoft-account"></a>Вопиюте в учетной записи Майкрософт

Выполнить приложение и нажмите **Войти в**. Появляется опция входним в Microsoft. При нажатии на кнопку Майкрософт вы перенаправляются в корпорацию Майкрософт для проверки подлинности. После входа в учетную запись Майкрософт вам будет предложено получить доступ к вашей информации:

Нажмите **Да,** и вы будете перенаправлены обратно на веб-сайт, где вы можете установить электронную почту.

Теперь вы вошли в систему с помощью учетных данных Майкрософт:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Устранение неполадок

* Если поставщик учетной записи Майкрософт перенаправляет вас на строку на странице ошибки, `#` обратите внимание на параметры строки запроса об ошибке и строке запроса описания непосредственно после (хэштег) в Uri.

  Хотя сообщение об ошибке, кажется, указывает на проблему с аутентификацией Майкрософт, наиболее распространенной причиной является то, что приложение Uri не соответствует ни одной из **URI,redirect,** указанных для **веб-платформы.**
* Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.* Шаблон проекта, используемый в этом примере, гарантирует, что это сделано.
* Если база данных сайта не была создана при применении первоначальной миграции, при *обработке ошибки запроса операция базы данных сработала.* Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.

## <a name="next-steps"></a>Следующие шаги

* Эта статья показала, как вы можете проверить подлинность с Microsoft. Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)

* Как только вы опубликуете свой веб-сайт в веб-приложении Azure, создайте новые секреты клиента на портале разработчиков Майкрософт.

* Установите `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` настройки приложения на портале Azure. Система конфигурации настроена на чтение ключей из переменных среды.
