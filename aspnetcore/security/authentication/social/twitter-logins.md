---
title: Внешняя установка входной системы Twitter с ASP.NET Core
author: rick-anderson
description: Этот учебник демонстрирует интеграцию аутентификации пользователя учетной записи Twitter в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277292"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Внешняя установка входной системы Twitter с ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом примере показано, как дать возможность пользователям [войти в свою учетную запись Twitter с](https://dev.twitter.com/web/sign-in/desktop-browser) помощью образца ASP.NET проекта Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index)

## <a name="create-the-app-in-twitter"></a>Создать приложение в Твиттере

* Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet.

* Перейдите [https://apps.twitter.com/](https://apps.twitter.com/) и войти в систему. Если у вас еще нет учетной записи Twitter, используйте ссылку **[Sign up для](https://twitter.com/signup)** ее создания.

* Выберите **Создать приложение**. Заполните **имя приложения**, **описание приложения** и общедоступный **веб-сайт** URI (это может быть временным, пока вы не зарегистрируете доменное имя):

* Проверьте поле рядом **с включить войти в с Щебетать**

* Microsoft.AspNetCore.Identity требует, чтобы пользователи имели адрес электронной почты по умолчанию. Перейдите на вкладку **«Разрешения»,** нажмите кнопку **«Отображить»** и проверьте поле рядом с **адресом электронной почты Запроса от пользователей.**

* Введите свой `/signin-twitter` URI развития с приложением к полю **URL-адресов Callback** (например: `https://webapp128.azurewebsites.net/signin-twitter`). Схема аутентификации Twitter, настроенная позже `/signin-twitter` в этом примере, будет автоматически обрабатывать запросы на маршруте для реализации потока OAuth.

  > [!NOTE]
  > Сегмент `/signin-twitter` URI устанавливается как обратный вызов по умолчанию поставщика аутентификации Twitter. Вы можете изменить по умолчанию обратный вызов URI при настройке Twitter аутентификации промежуточного через унаследованные [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) собственности [класса TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Заполните оставшуюся форму и выберите **Создать**. Отображаются новые сведения о приложении:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Храните ключ API-клавишу и секрет потребителя Twitter

Храните чувствительные настройки, такие как ключ API-исекретный ключ Twitter потребителя и секрет с [Secret Manager.](xref:security/app-secrets) Для этого примера используйте следующие шаги:

1. Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).
1. Храните чувствительные настройки в местном секретном магазине с ключами `Authentication:Twitter:ConsumerKey` секретов и: `Authentication:Twitter:ConsumerSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Эти маркеры можно найти на вкладке **«Ключи и токены доступа»** после создания нового приложения Twitter:

## <a name="configure-twitter-authentication"></a>Настройка twitter аутентификации

Добавьте службу `ConfigureServices` Twitter в метод в *Startup.cs* файле:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией Twitter, можно ознакомиться с ссылкой на [API Twitter.](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Это может быть использовано для запроса различной информации о пользователе.

## <a name="sign-in-with-twitter"></a>Вопиюте с Twitter

Запустите приложение и выберите **Войти в**. Появляется опция для вху-на-Айва с Twitter:

Нажав на **Twitter** перенаправляет на Twitter для проверки подлинности:

После ввода учетных данных Twitter, вы перенаправитесь обратно на веб-сайт, где вы можете настроить электронную почту.

Теперь вы вошли в систему с помощью учетных данных Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Устранение неполадок

* **ASP.NET core 2.x только:** Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.* Шаблон проекта, используемый в этом примере, гарантирует, что это сделано.
* Если база данных сайта не была создана при применении первоначальной миграции, при *обработке ошибки запроса операция базы данных сработала.* Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* Эта статья показала, как вы можете проверить подлинность с Twitter. Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)

* Как только вы опубликуете свой веб-сайт в `ConsumerSecret` веб-приложении Azure, вы должны сбросить на портале разработчиков Twitter.

* Установите `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` настройки приложения на портале Azure. Система конфигурации настроена на чтение ключей из переменных среды.
