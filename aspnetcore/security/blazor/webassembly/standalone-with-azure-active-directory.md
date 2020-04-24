---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111205"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2. Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.

Чтобы создать Blazor изолированное приложение для сборки, использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности:

[Создание клиента AAD и веб-приложения](/azure/active-directory/develop/v2-overview):

Зарегистрируйте приложение AAD в области **Azure Active Directory** > **Регистрация приложений** портал Azure:

1. Укажите **имя** приложения (например, ** Blazor AAD клиента**).
1. Выберите **Поддерживаемые типы учетных записей**. Вы можете выбрать **учетные записи в этом каталоге организации только** для этого интерфейса.
1. Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.
1. Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .
1. Выберите **Зарегистрировать**.

В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:

1. Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.
1. Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.
1. Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.
1. Нажмите кнопку **Сохранить**.

Запишите следующие сведения:

* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)
* Идентификатор каталога (идентификатор клиента) (например, `22222222-2222-2222-2222-222222222222`)

Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`). Имя папки также станет частью имени проекта.

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
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения. Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.

## <a name="access-token-scopes"></a>Области токенов доступа

Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API. Чтобы подготавливать маркер в процессе входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:

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

Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

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
* <xref:security/authentication/azure-active-directory/index>
* [Документация по платформе удостоверений Майкрософт](/azure/active-directory/develop/)
