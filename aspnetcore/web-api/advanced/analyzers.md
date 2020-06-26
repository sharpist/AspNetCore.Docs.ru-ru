---
title: Использование анализаторов веб-API
author: pranavkm
description: См. сведения о пакете анализаторов веб-API MVC ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1f44fd65836cc42ffb2303890eefb053b61c4c30
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399469"
---
# <a name="use-web-api-analyzers"></a>Использование анализаторов веб-API

ASP.NET Core 2.2 и более поздних версий предоставляет пакет анализаторов MVC, предназначенный для использования с проектами веб-API. Анализаторы работают с контроллерами, которые помечены с помощью <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>. Кроме того, к ним применяются [соглашения об использовании веб-API](xref:web-api/advanced/conventions).

Пакет анализаторов уведомляет вас о любом действии контроллера, которое:

* возвращает необъявленный код состояния;
* возвращает необъявленный успешный результат;
* документирует код состояния, который не был получен;
* включает проверку явной модели.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Создание ссылки на пакет анализатора

В ASP.NET Core 3.0 или более поздней версии анализаторы включены в пакет SDK для .NET Core. Чтобы включить анализатор в проекте, включите свойство `IncludeOpenAPIAnalyzers` в файл проекта:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Установка пакета

Установите пакет [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet с помощью одного из следующих методов:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

В окне **Консоль диспетчера пакетов**
  * Перейдите к разделу **Просмотр** > **других** > **консолей диспетчера пакетов**Windows.
  * Перейдите в каталог, в котором находится файл *ApiConventions.csproj*
  * Выполните следующую команду:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните правой кнопкой мыши папку *пакеты* в **панель решения** > **Добавить пакеты..**..
* Задайте в раскрывающемся списке " **Добавить пакеты** " **источник** "NuGet.org".
* В поле поиска введите "Microsoft.AspNetCore.Mvc.Api.Analyzers".
* В области результатов выберите пакет "Microsoft.AspNetCore.Mvc.Api.Analyzers", а затем нажмите кнопку **Добавить пакет**.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во **встроенном терминале** выполните следующую команду.

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

Выполните следующую команду:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Анализаторы для соглашений об использовании веб-API

Документы OpenAPI содержат коды состояний и типы ответов, которые может возвращать действие. В ASP.NET Core MVC атрибуты, такие как <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> и <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>, используются для документирования действия. <xref:tutorials/web-api-help-pages-using-swagger> позволяет более подробно документировать веб-API.

Один из анализаторов в пакете проверяет контроллеры, которые аннотированы <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, и определяет действия, которые не полностью документируют их ответы. Рассмотрим следующий пример.

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Предыдущее действие документирует тип возврата "Успех HTTP 200", но не документирует код состояния "Ошибка HTTP 404". Анализатор сообщает об отсутствующем документировании для кода состояния HTTP 404 в виде предупреждения. Эту проблему можно устранить.

![Предупреждение анализатора](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
