---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 95c16bcd8da22792b27b3aaaf8632b2206372270
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150061"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Чтобы создать изолированное приложение для веб- Blazor сборки, использующее [учетные записи Майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности:

1. [Создание клиента AAD и веб-приложения](/azure/active-directory/develop/v2-overview)

   Зарегистрируйте приложение AAD в области **Azure Active Directory** > **Регистрация приложений** портал Azure:

   1 \. Укажите **имя** приложения (например, ** Blazor AAD клиента**).<br>
   2 \. В списке **Поддерживаемые типы учетных записей**выберите **учетные записи в любом организационном каталоге**.<br>
   3 \. Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.<br>
   4 \. Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .<br>
   5 \. Выберите **Зарегистрировать**.

   В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:

   1 \. Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.<br>
   2 \. Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.<br>
   3 \. Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.<br>
   4 \. Нажмите кнопку **Сохранить**.

   Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).

1. Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`). Имя папки также станет частью имени проекта.

После создания приложения вы сможете:

* Войдите в приложение, используя учетную запись Майкрософт.
* Запросите маркеры доступа для API-интерфейсов Майкрософт, используя Blazor тот же подход, что и для автономных приложений, при условии, что приложение настроено правильно. Дополнительные сведения см. [в разделе Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Пакет проверки подлинности

При создании приложения для использования рабочих или учебных учетных записей`SingleOrg`приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.

При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.

`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.

## <a name="authentication-service-support"></a>Поддержка службы проверки подлинности

Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом. Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации учетных записей Майкрософт при регистрации приложения.

Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

Пример.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>Области токенов доступа

Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API. Чтобы настроить маркер доступа как часть потока входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел. Например, портал Azure может предоставить один из следующих форматов URI области:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Укажите URI области без схемы и узла:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :

* [Запрос дополнительных маркеров доступа](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Присоединение маркеров к исходящим запросам](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Файл импорта

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Страница индексации

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Компонент приложения

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Компонент Редиректтологин

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Компонент Логиндисплай

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Компонент проверки подлинности

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/blazor/webassembly/additional-scenarios>
* [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
