---
title: Настройка внешнего входа Facebook в ASP.NET Core
author: rick-anderson
description: Руководство с примерами кода, демонстрирующими интеграцию аутентификации пользователя с учетной записью Facebook с существующим ASP.NET Core приложением.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: 11ddc7314a694446d488da6ef1b2e3423bf7241a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777037"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Настройка внешнего входа Facebook в ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

В этом учебнике с примерами кода показано, как включить вход пользователей с помощью учетной записи Facebook, используя пример проекта ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index). Начнем с создания идентификатора приложения Facebook, следуя [официальным действиям](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Создание приложения в Facebook

* Добавьте в проект пакет NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .

* Перейдите на страницу [приложения Facebook Developers](https://developers.facebook.com/apps/) и выполните вход. Если у вас еще нет учетной записи Facebook, используйте ссылку **зарегистрироваться для Facebook** на странице входа, чтобы создать ее.  После получения учетной записи Facebook следуйте инструкциям по регистрации в качестве разработчика Facebook.

* В меню **Мои приложения** выберите пункт **создать приложение** , чтобы создать новый идентификатор приложения.

   ![Портал Facebook для разработчиков открыт в Microsoft ребр](index/_static/FBMyApps.png)

* Заполните форму и нажмите кнопку **CREATE App ID (создать идентификатор приложения** ).

  ![Создание новой формы идентификатора приложения](index/_static/FBNewAppId.png)

* В карточке нового приложения выберите **Добавить продукт**.  На карточке **входа Facebook** щелкните **настроить** . 

  ![Страница «Настройка продукта»](index/_static/FBProductSetup.png)

* Мастер **быстрого** запуска запускается с параметром **выбрать платформу** в качестве первой страницы. Пропустите мастер, щелкнув ссылку **Параметры** **входа Facebook** в меню в левом нижнем углу экрана:

  ![Пропустить Быстрое начало](index/_static/FBSkipQuickStart.png)

* Отобразится страница **параметров OAuth клиента** :

  ![Страница параметров OAuth клиента](index/_static/FBOAuthSetup.png)

* Введите универсальный код ресурса (URI) для разработки с */сигнин-фацебук* , добавленным в **допустимое поле URI перенаправления OAuth** (например: `https://localhost:44320/signin-facebook`). Проверка подлинности Facebook, настроенная далее в этом руководстве, автоматически обрабатывает запросы по маршруту */сигнин-фацебук* для реализации потока OAuth.

> [!NOTE]
> URI */сигнин-фацебук* задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Facebook. URI обратного вызова по умолчанию можно изменить при настройке по промежуточного слоя проверки подлинности Facebook с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [фацебукоптионс](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Щелкните **Сохранить изменения**.

* Щелкните ссылку **Параметры** > **Базовая** ссылка в области навигации слева.

  На этой странице запишите `App ID` и ваш. `App Secret` Вы добавите оба варианта в приложение ASP.NET Core в следующем разделе:

* При развертывании сайта необходимо повторно посетить страницу настройки **имени входа Facebook** и зарегистрировать новый общедоступный URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Хранение идентификатора и секрета приложения Facebook

Храните конфиденциальные параметры, такие как идентификатор приложения Facebook и значения секрета, с помощью [диспетчера секретов](xref:security/app-secrets). Для этого примера выполните следующие действия.

1. Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).
1. Храните конфиденциальные параметры в локальном хранилище секретов с секретными `Authentication:Facebook:AppId` ключами `Authentication:Facebook:AppSecret`и:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Настройка проверки подлинности Facebook

Добавьте службу Facebook в `ConfigureServices` метод в файле *Startup.CS* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Вход с помощью Facebook

* Запустите приложение и выберите **Вход**. 
* В разделе **использовать другую службу для входа в систему**выберите Facebook.
* Вы будете перенаправлены на **Facebook** для проверки подлинности.
* Введите учетные данные Facebook.
* Вы будете перенаправлены на сайт, где можно задать свой адрес электронной почты.

Теперь вы выполнили вход с использованием учетных данных Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Реакция на отмену авторизации внешнего входа

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>может предоставить путь перенаправления к агенту пользователя, когда пользователь не утвердит запрошенный запрос на авторизацию.

Следующий код задает для `AccessDeniedPath` `"/AccessDeniedPathInfo"`значение:

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Рекомендуется, `AccessDeniedPath` чтобы страница содержала следующие сведения:

*  Удаленная проверка подлинности была отменена.
* Для этого приложения требуется проверка подлинности.
* Чтобы повторить попытку входа, выберите ссылку для входа.

### <a name="test-accessdeniedpath"></a>Тестовый Акцессдениедпас

* Перейдите по адресу [Facebook.com](https://www.facebook.com/)
* Если вы вошли в систему, необходимо выйти из нее.
* Запустите приложение и выберите пункт Вход в Facebook.
* Выберите **не сейчас**. Вы будете перенаправлены на указанную `AccessDeniedPath` страницу.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности Facebook, см. в справочнике по API [фацебукоптионс](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) . Параметры конфигурации можно использовать для:

* Запросите другие сведения о пользователе.
* Добавьте аргументы строки запроса, чтобы настроить процедуру входа.

## <a name="troubleshooting"></a>Устранение неполадок

* **Только ASP.NET Core 2. x:** Если Identity параметр не настроен с `services.AddIdentity` помощью `ConfigureServices`вызова в, попытка проверки подлинности приведет к тому, что будет получено исключение *"сигнинсчеме"*. Шаблон проекта, используемый в этом руководстве, гарантирует, что это будет сделано.
* Если база данных сайта не была создана путем применения первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* . Выберите **Применить миграции** , чтобы создать базу данных и обновить, чтобы продолжить выполнение после ошибки.

## <a name="next-steps"></a>Дальнейшие действия

* В этой статье показано, как можно пройти проверку подлинности с помощью Facebook. Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).

* После публикации веб-сайта в веб-приложение Azure необходимо сбросить его `AppSecret` на портале разработчика Facebook.

* Задайте параметры `Authentication:Facebook:AppId` приложения `Authentication:Facebook:AppSecret` и в портал Azure. Система конфигурации настроена на чтение ключей из переменных среды.
