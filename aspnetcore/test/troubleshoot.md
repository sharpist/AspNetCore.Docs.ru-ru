---
title: Устранение неполадок и отладка проектов ASP.NET Core
author: Rick-Anderson
description: Устранение неполадок при возникновении ошибок и предупреждений в проектах ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: 74b494f6d16637a54ab7f1b5c0cf9fbb86248fe7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776621"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Устранение неполадок и отладка проектов ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Рекомендации по устранению неполадок приведены по следующим ссылкам:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Конференция NDC (Лондон, 2018): диагностика проблем в приложениях ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Блог об ASP.NET. Устранение проблем ASP.NET Core, связанных с производительностью](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Предупреждение пакета SDK для .NET Core

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Установлены как 32-разрядная, так и 64-разрядная версии пакета SDK для .NET Core

В диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.

> Установлены как 32-разрядная, так и 64-разрядная версии пакета SDK для .NET Core. Отображаются только шаблоны 64-разрядных версий, установленных в папке "C:\\Program Files\\dotnet\\sdk\\".

Это предупреждение появляется при одновременной установке 32-разрядной (x86) и 64-разрядной (x64) версий [пакета SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core). Ниже перечислены распространенные причины, по которым могут быть установлены обе версии:

* вы первоначально загрузили установщик пакета SDK для .NET Core на 32-разрядном компьютере, а затем скопировали его и установили на 64-разрядном компьютере;
* 32-разрядная версия пакета SDK для .NET Core была установлена другим приложением;
* была загружена и установлена неправильная версия.

Удалите 32-разрядную версию пакета SDK для .NET Core, чтобы устранить это предупреждение. Для удаления перейдите в **Панель управления** > **Программы и компоненты** > **Удаление или изменение программы**. Если вы понимаете, почему возникает предупреждение и его последствия, вы можете проигнорировать это предупреждение.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Пакет SDK для .NET Core установлен в нескольких расположениях

В диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.

> Пакет SDK для .NET Core установлен в нескольких расположениях. Отображаются только шаблоны пакетов SDK, установленных в папке "C:\\Program Files\\dotnet\\sdk\\".

Это сообщение появляется при наличии хотя бы одной установки пакета SDK для .NET Core в каталоге за пределами *C:\\Program Files\\dotnet\\sdk\\* . Обычно это происходит, когда пакет SDK для .NET Core развертывается на компьютере с помощью команды копировать/вставить, а не с помощью установщика MSI.

Удалите все 32-разрядные версии пакетов SDK и среды выполнения .NET Core, чтобы устранить это предупреждение. Для удаления перейдите в **Панель управления** > **Программы и компоненты** > **Удаление или изменение программы**. Если вы понимаете, почему возникает предупреждение и его последствия, вы можете проигнорировать это предупреждение.

### <a name="no-net-core-sdks-were-detected"></a>Пакеты SDK для .NET Core не обнаружены

* В Visual Studio в диалоговом окне **Новый проект** для ASP.NET Core может отобразиться следующее предупреждение.

  > Пакеты SDK для .NET Core не обнаружены. Они должны быть включены в переменную среды `PATH`.

* При выполнении команды `dotnet` предупреждение выглядит следующим образом.

  > Не удалось найти установленные пакеты SDK dotnet.

Эти предупреждения появляются, когда переменная среды `PATH` не содержит расположение пакетов SDK для .NET Core на компьютере. Для решения этой проблемы сделайте следующее:

* Установите пакет SDK для .NET Core. Получите последнюю версию установщика в [разделе загрузок .NET](https://dotnet.microsoft.com/download).
* Убедитесь, что переменная среды `PATH` содержит расположение, в которое установлен пакет SDK (`C:\Program Files\dotnet\` для 64-разрядной версии — x64, или `C:\Program Files (x86)\dotnet\` для 32-разрядной версии — x86). Установщик пакета SDK обычно устанавливает значение `PATH`. Всегда устанавливайте на одном компьютере пакеты SDK и среды выполнения одинаковой разрядности.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Отсутствует пакет SDK после установки пакета размещения .NET Core

Установка [пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) изменяет переменную среды `PATH` при установке среды выполнения .NET Core, чтобы она указывала на 32-разрядную (x86) версию .NET Core (`C:\Program Files (x86)\dotnet\`). Это может привести к отсутствию пакетов SDK при использовании 32-разрядной (x86) команды .NET Core `dotnet` ([Пакеты SDK для .NET Core не обнаружены](#no-net-core-sdks-were-detected)). Для решения этой проблемы в переменной `PATH` переместите значение `C:\Program Files\dotnet\` в положение перед `C:\Program Files (x86)\dotnet\`.

## <a name="obtain-data-from-an-app"></a>Получение данных из приложения

Если приложение может отвечать на запросы, можно получить следующие данные из приложения с помощью ПО промежуточного слоя:

* Запрос — метод, схема, узел, свойство pathbase, путь, строка запроса, заголовки
* Подключение — удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента
* Identity &ndash;. Имя, отображаемое имя
* Параметры конфигурации
* Переменные среды

Поместите следующий код [ПО промежуточного слоя](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в начале конвейера обработки запроса метода `Startup.Configure`. Среда проверяется перед выполнением ПО промежуточного слоя, чтобы гарантировать выполнение кода только в среде разработки.

Чтобы получить среду, используйте один из следующих подходов.

* Вставьте `IHostingEnvironment` в метод `Startup.Configure` и проверьте окружение с помощью локальной переменной. Этот подход демонстрируется в следующем образце кода.

* Назначьте среду свойству в классе `Startup`. Проверьте среду с помощью свойства (например, `if (Environment.IsDevelopment())`).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Отладка приложений ASP.NET Core

Следующие ссылки содержат сведения об отладке приложений ASP.NET Core.

* [Отладка ASP Core в Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Отладка .NET Core в UNIX через SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Краткое руководство. Отладка в ASP.NET с помощью отладчика Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Дополнительные сведения об отладке см. в [этой статье об ошибке на GitHub ](https://github.com/dotnet/AspNetCore.Docs/issues/2960).
