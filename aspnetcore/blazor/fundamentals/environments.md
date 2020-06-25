---
title: Среды ASP.NET Core Blazor
author: guardrex
description: Сведения о средах в Blazor, в том числе о настройке среды для приложения Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: 203f29ce606a313463e416b068177ce02acd6231
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103329"
---
# <a name="aspnet-core-blazor-environments"></a>Среды ASP.NET Core Blazor

> [!NOTE]
> Эта статья относится к Blazor WebAssembly. Общие рекомендации по настройке приложений ASP.NET Core см. в разделе <xref:fundamentals/environments>.

При локальном запуске приложения среда по умолчанию имеет значение Development. При публикации приложения среда по умолчанию имеет значение Production.

Размещенное приложение Blazor WebAssembly выбирает среду на сервере через ПО промежуточного слоя, которое передает данные о среде в браузер, добавляя заголовок `blazor-environment`. В заголовке содержится среда. Размещенное приложение Blazor и серверное приложение совместно используют одну и ту же среду. Дополнительные сведения, в том числе о настройке среды, см. на странице <xref:fundamentals/environments>.

Для автономного приложения, выполняемого локально, сервер разработки добавляет заголовок `blazor-environment`, чтобы указать среду разработки. Чтобы указать среду для других сред размещения, добавьте заголовок `blazor-environment`.

В следующем примере для IIS добавьте пользовательский заголовок в опубликованный *файл Web. config*. Файл *web.config* размещен в папке *bin/Release/{TARGET FRAMEWORK}/publish*:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Сведения об использовании пользовательского файла *web.config* для служб IIS, который не перезаписывается при публикации приложения в папку *Publish*, см. в разделе <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.

Чтобы получить среду приложения в компоненте, вставьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> и прочтите свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Во время запуска <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> раскрывает <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, которое позволяет разработчикам реализовать в своем коде логику для конкретной среды:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Следующие удобные методы расширения допускают проверку текущей среды на признак среды разработки, тестирования, коммерческой среды или пользовательской среды:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> можно использовать во время запуска, если служба <xref:Microsoft.AspNetCore.Components.NavigationManager> недоступна.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/environments>
