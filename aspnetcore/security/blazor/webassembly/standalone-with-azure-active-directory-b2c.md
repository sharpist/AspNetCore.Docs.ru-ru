---
title: Защищайте Blazor автономное приложение ASP.NET Core WebAssembly с помощью Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123459"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Защищайте Blazor автономное приложение ASP.NET Core WebAssembly с помощью Azure Active Directory B2C

[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Для создания Blazor автономного приложения WebAssembly, использующему Для проверки подлинности [Active Directory (AAD) B2C,](/azure/active-directory-b2c/overview) используется приложение Для получения подлинности:

1. Следуйте инструкциям по следующим темам для создания арендатора и регистрации веб-приложения на портале Azure:

   * [Создайте aAD B2C арендатора](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Запись следующая информация:

     1\. AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`который включает в себя задний слэш)<br>
     2\. AAD B2C Арендатор домена `contoso.onmicrosoft.com`(например, )

   * [Регистрация веб-приложения](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Сделать следующие выборы во время регистрации приложения:

     1\. Установите **Web App / Web API** для **Yes**.<br>
     2\. Установить **Разрешить неявный поток** **да**.<br>
     3\. Добавить **URL-адрес ответа** `https://localhost:5001/authentication/login-callback`.

     Запись идентификатора приложения (например, `11111111-1111-1111-1111-111111111111`id клиента).

   * [Создание пользовательских потоков](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Создание регистрации и вступления в поток пользователя.

     Как минимум, выберите атрибут **приложения Претензии** > **Display Name** для заполнения `context.User.Identity.Name` `LoginDisplay` компонента *(Общий/LoginDisplay.razor*).

     Запись регистрации и входе в пользовательское имя, созданное `B2C_1_signupsignin`для приложения (например, ).

1. Замените заполнителей в следующей команде информацией, записанной ранее, и выполните команду в командном корпусе:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`). Имя папки также становится частью названия проекта.

## <a name="authentication-package"></a>Пакет аутентификации

Когда приложение создается для использования индивидуальной`IndividualB2C`учетной записи B2C ( ), приложение`Microsoft.Authentication.WebAssembly.Msal`автоматически получает ссылку на пакет для [библиотеки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( ). Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.

При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.

Пакет `Microsoft.Authentication.WebAssembly.Msal` транзитно добавляет `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.

## <a name="authentication-service-support"></a>Поддержка службы аутентификации

Поддержка аутентификации пользователей регистрируется `AddMsalAuthentication` в сервисном `Microsoft.Authentication.WebAssembly.Msal` контейнере с методом расширения, предусмотренным пакетом. Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком идентификационных данных (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

Метод `AddMsalAuthentication` принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации Azure Portal AAD при регистрации приложения.

## <a name="access-token-scopes"></a>Области маркеров доступа

Шаблон Blazor WebAssembly не настраивает приложение автоматически, чтобы запросить токен доступа для защищенного API. Чтобы предоставить токен как часть потока ввоза, добавьте область к области `MsalProviderOptions`маркеров доступа по умолчанию:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Если портал Azure предоставляет область URI и **приложение выбрасывает необработанное исключение,** когда получает *401 несанкционированный* ответ от API, попробуйте использовать область URI, которая не включает схему и узел. Например, портал Azure может предоставить один из следующих форматов URI:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Поставка области URI без схемы и хозяина:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>ПеренаправлениеКомпонентToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Компонент LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Компонент аутентификации

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Запрос дополнительных токенов доступа](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Руководство по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
