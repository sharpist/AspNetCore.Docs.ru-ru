---
title: Внешняя установка входа Facebook в ASP.NET Core
author: rick-anderson
description: Учебник с примерами кода, демонстрирующим интеграцию аутентификации пользователя учетной записи Facebook в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277305"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Внешняя установка входа Facebook в ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

В этом учебнике с примерами кода показано, как дать возможность пользователям войти в систему со своей учетной записью Facebook, используя образец ASP.NET проекта Core 3.0, созданного на [предыдущей странице.](xref:security/authentication/social/index) Мы начинаем с создания Идентификатора приложения Facebook, следуя [официальным шагам.](https://developers.facebook.com)

## <a name="create-the-app-in-facebook"></a>Создать приложение на Facebook

* Добавьте в проект пакет [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet.

* Перейдите на страницу [приложения Facebook Developers](https://developers.facebook.com/apps/) и войдите в систему. Если у вас еще нет учетной записи Facebook, используйте **ссылку на Facebook** на странице входа для ее создания.  Если у вас есть учетная запись Facebook, следуйте инструкциям, чтобы зарегистрироваться в качестве разработчика Facebook.

* Из меню **My Apps** выберите **Создать приложение** для создания нового идентификатора приложения.

   ![Facebook для разработчиков портал открыт в Microsoft Edge](index/_static/FBMyApps.png)

* Заполните форму и нажмите кнопку **Create App ID.**

  ![Создание новой формы идентификатора приложения](index/_static/FBNewAppId.png)

* На новой карте Приложения выберите **Добавить продукт.**  На карте **Входа в Facebook** нажмите **«Настройка»** 

  ![Страница настройки продукта](index/_static/FBProductSetup.png)

* Мастер **квикстарта** запускает с **выберите платформу** в качестве первой страницы. Обойти мастера на данный момент, нажав **на FaceBook Логин** **Настройки** в меню в левом нижнем нижнем:

  ![Перейти Быстрый старт](index/_static/FBSkipQuickStart.png)

* Вам предоставляется страница **«Настройки клиента OAuth»:**

  ![Страница Настройки клиента OAuth](index/_static/FBOAuthSetup.png)

* Введите свою разработку URI с */signin-facebook,* приложенное в `https://localhost:44320/signin-facebook`действительное поле **OAuth Redirect URIs** (например: ). Проверка подлинности Facebook, настроенная позже в этом учебнике, будет автоматически обрабатывать запросы на *маршруте /signin-facebook* для реализации потока OAuth.

> [!NOTE]
> URI */signin-facebook* установлен как обратный вызов по умолчанию поставщика аутентификации Facebook. Вы можете изменить по умолчанию обратный вызов URI при настройке Facebook аутентификации промежуточного через унаследованные [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) собственности [класса FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Нажмите кнопку **Сохранить изменения**.

* Нажмите **Настройки** > **Основная** ссылка в левой навигации.

  На этой странице, сделать `App ID` заметку вашего и вашего `App Secret`. Оба приложения ASP.NET Core в следующем разделе:

* При развертывании сайта необходимо пересмотреть страницу установки **входа в Facebook** и зарегистрировать новую общедоступную URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Храните идентификатор идентификатор приложения Facebook и секрет

Храните чувствительные настройки, такие как идентификатор приложения Facebook и секретные значения с [помощью Secret Manager.](xref:security/app-secrets) Для этого примера используйте следующие шаги:

1. Инициализация проекта для тайного хранения в инструкции на [Включить секретного хранения](xref:security/app-secrets#enable-secret-storage).
1. Храните чувствительные настройки в местном секретном магазине с секретными ключами `Authentication:Facebook:AppId` и: `Authentication:Facebook:AppSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Настройка facebook аутентификация

Добавьте службу `ConfigureServices` Facebook в метод в *файле Startup.cs:*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Вопиюте с Facebook

* Запустите приложение и выберите **Войти в**. 
* **Под используйте другую службу для входа в систему.**, выберите Facebook.
* Вы перенаправлены на **Facebook** для проверки подлинности.
* Введите свои учетные данные на Facebook.
* Вы перенаправлены обратно на ваш сайт, где вы можете настроить электронную почту.

Теперь вы вошли в систему, используя свои учетные данные Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Реагировать на отмену разрешения на внешний вйза

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>может обеспечить перенаправление агента пользователя, если пользователь не одобряет запрашиваемый запрос на авторизацию.

Следующий код `AccessDeniedPath` устанавливает: `"/AccessDeniedPathInfo"`

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Мы рекомендуем, чтобы `AccessDeniedPath` страница содержала следующую информацию:

*  Удаленная аутентификация была отменена.
* Это приложение требует проверки подлинности.
* Чтобы попробовать войти снова, выберите ссылку входа.

### <a name="test-accessdeniedpath"></a>Тест AccessDeniedPath

* Перейдите к [facebook.com](https://www.facebook.com/)
* Если вы вписаны, вы должны выйти.
* Запустите приложение и выберите Facebook войти в систему.
* Выберите **не сейчас**. Вы перенаправляетсяе `AccessDeniedPath` на указанную страницу.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Для получения дополнительной информации о вариантах конфигурации, поддерживаемых аутентификацией Facebook, можно ознакомиться с ссылкой на [API Facebook.](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Параметры конфигурации можно использовать для:

* Запросить различную информацию о пользователе.
* Добавьте аргументы строки запроса, чтобы настроить опыт входа.

## <a name="troubleshooting"></a>Устранение неполадок

* **ASP.NET core 2.x только:** Если идентификация не настроена `services.AddIdentity` `ConfigureServices`при вызове, попытка проверки подлинности приведет к тому, что *опция ArgumentException: опция SignInScheme должна быть предоставлена.* Шаблон проекта, используемый в этом учебнике, гарантирует, что это сделано.
* Если база данных сайта не была создана при применении первоначальной миграции, *при обработке ошибки запроса операция базы данных сработала.* Нажмите **Применить миграции** для создания базы данных и обновления, чтобы продолжить прошлое ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* Эта статья показала, как вы можете проверить подлинность с Facebook. Вы можете следовать аналогичному подходу к аутентификации с другими поставщиками, перечисленными на [предыдущей странице.](xref:security/authentication/social/index)

* Как только вы опубликуете свой веб-сайт в `AppSecret` веб-приложении Azure, вы должны сбросить на портале разработчиков Facebook.

* Установите `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` настройки приложения на портале Azure. Система конфигурации настроена на чтение ключей из переменных среды.
