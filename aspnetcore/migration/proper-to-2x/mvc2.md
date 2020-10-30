---
title: Миграция с ASP.NET на ASP.NET Core 2.0
author: isaac2004
description: Получите рекомендации по миграции существующих приложений ASP.NET MVC или веб-API на ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051334"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>Миграция с ASP.NET на ASP.NET Core 2.0

Автор [Айзек Левин](https://isaaclevin.com) (Isaac Levin)

Данная статья служит руководством по миграции приложений ASP.NET на ASP.NET Core 2.0.

## <a name="prerequisites"></a>Предварительные требования

Установите **одну** из следующих версий из центра [загрузки .NET: Windows](https://dotnet.microsoft.com/download):

* Пакет SDK для .NET Core
* Visual Studio для Windows
  * Рабочая нагрузка **ASP.NET и веб-разработка**
  * Рабочая нагрузка **Кроссплатформенная разработка .NET Core**

## <a name="target-frameworks"></a>Требуемые версии .NET Framework

Проекты ASP.NET Core 2.0 предлагают разработчикам гибкость работы с .NET Core и .NET Framework. Определить наиболее подходящую платформу поможет статья [Выбор между .NET Core и .NET Framework для серверных приложений](/dotnet/standard/choosing-core-framework-server).

При разработке для .NET Framework проекты должны ссылаться на отдельные пакеты NuGet.

Работа с .NET Core позволяет избавиться от многочисленных явных ссылок на пакеты, благодаря [метапакету](xref:fundamentals/metapackage) ASP.NET Core 2.0. Установите метапакет `Microsoft.AspNetCore.All` в свой проект.

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

При использовании метапакета никакие указанные по ссылкам пакеты с приложением не развертываются. Все эти ресурсы входят в хранилище среды выполнения .NET Core и предварительно компилируются для повышения производительности. <xref:fundamentals/metapackage>Дополнительные сведения см. в разделе.

## <a name="project-structure-differences"></a>Различия в структуре пакетов

В ASP.NET Core упрощен формат файла *.csproj* . Вот некоторые основные изменения.

* Чтобы файлы считались частью проекта, включать их явно теперь не требуется. Это уменьшает вероятность конфликтов слияния XML при работе в больших командах.
* GUID-ссылки на другие проекты не используются, что повышает удобочитаемость файла.
* Файл можно редактировать, не выгружая его в Visual Studio.

  ![Параметр изменения файла CSPROJ в контекстном меню в Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Замена файла Global.asax

В ASP.NET Core появился новый механизм для начальной загрузки приложения. Точкой входа для приложений ASP.NET стал файл *Global.asax* . Такие задачи, как конфигурация маршрута, а также регистрации фильтров и областей, теперь выполняются в файле *Global.asax* .

[!code-csharp[](samples/globalasax-sample.cs)]

При этом подходе приложение сопоставляется с сервером, на котором оно развертывается, таким образом, чтобы это не мешало реализации. Чтобы отделить приложение от сервера, был введен [OWIN](https://owin.org/), который обеспечивает более точный способ совместного использования нескольких платформ. OWIN предоставляет конвейер для добавления только необходимых модулей. Среда внешнего размещения принимает функцию [Startup](xref:fundamentals/startup) для настройки служб и конвейера обработки запросов приложения. `Startup` регистрирует набор ПО промежуточного слоя в приложении. Для каждого запроса приложение вызывает каждый из компонентов ПО промежуточного слоя по заглавному указателю связанного списка на существующий набор обработчиков. Каждый компонент ПО промежуточного слоя может добавлять в конвейер обработки запросов один обработчик или несколько. Это достигается путем возвращения ссылки на обработчик, который представляет собой новый заголовок списка. Каждый обработчик отвечает за запоминание и вызов следующего обработчика в списке. При работе с ASP.NET Core точкой входа в приложения становится `Startup`, и зависимость от файла *Global.asax* исчезает. Используя OWIN с .NET Framework, применяйте для конвейера код следующего вида:

[!code-csharp[](samples/webapi-owin.cs)]

Он определяет ваши маршруты по умолчанию и изначально предусматривает XmlSerialization по Json. При необходимости добавьте другое ПО промежуточного слоя для этого конвейера (загрузка служб, параметры конфигурации, статические файлы и т. д.).

ASP.NET Core использует аналогичный подход, но не требует OWIN для обработки запроса. Вместо этого применяется метод *Program.cs* `Main` (как в консольных приложениях) и `Startup` загружается через него.

[!code-csharp[](samples/program.cs)]

`Startup` должен включать метод `Configure`. В `Configure` добавьте в конвейер необходимое ПО промежуточного слоя. В следующем примере (на основе шаблона веб-сайта по умолчанию) используются несколько методов расширения для настройки конвейера с поддержкой следующих компонентов.

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* Страницы ошибок
* Статические файлы
* ASP.NET Core MVC
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

Сервер и приложение разделены, что позволит вам в будущем легко перейти на другую платформу.

Более подробные справочные сведения о ASP.NET Core запуске и по промежуточного слоя см <xref:fundamentals/startup> . в разделе.

## <a name="storing-configurations"></a>Конфигурации хранения

ASP.NET поддерживает параметры хранения. Эти параметры используются, например, для поддержки среды, в которой развертываются приложения. Как правило, все пользовательские пары ключей и значений хранятся в разделе `<appSettings>` файла *Web.config* :

[!code-xml[](samples/webconfig-sample.xml)]

Приложения считывают эти параметры с помощью коллекции `ConfigurationManager.AppSettings` в пространстве имен `System.Configuration`:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core может сохранять данные конфигурации для приложения из любого файла и загружать их в процессе начальной загрузки ПО промежуточного слоя. В шаблонах проекта используется файл по умолчанию *appsettings.json* :

[!code-json[](samples/appsettings-sample.json)]

Загрузка этого файла в экземпляр `IConfiguration` в вашем приложении выполняется в файле *Startup.cs* :

[!code-csharp[](samples/startup-builder.cs)]

Для получения этих параметров приложение считывает данные из `Configuration`:

[!code-csharp[](samples/read-appsettings.cs)]

Существуют расширения, повышающие надежность этого подхода, например, [внедрение зависимостей](xref:fundamentals/dependency-injection) позволяет загружать эти значения в службу. Метод внедрения зависимостей предоставляет строго типизированный набор объектов конфигурации.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

**Примечание.** Более подробные справочные сведения о ASP.NET Core конфигурации см. в разделе <xref:fundamentals/configuration/index> .

## <a name="native-dependency-injection"></a>Собственные функции внедрения зависимостей

При сборке больших, масштабируемых приложений важно обеспечить слабые взаимозависимости между компонентами и службами. [Внедрение зависимостей](xref:fundamentals/dependency-injection) — это популярная методика для достижения этого, и это собственный компонент ASP.NET Core.

В приложениях ASP.NET разработчики используют библиотеку сторонних производителей для реализации внедрения зависимостей. Одна из таких библиотек, [Unity](https://github.com/unitycontainer/unity), входит в шаблоны и рекомендации Майкрософт.

Примером настройки внедрения зависимостей с помощью Unity является реализация `IDependencyResolver` , которая создает оболочку для `UnityContainer` :

[!code-csharp[](samples/sample8.cs)]

Создайте экземпляр `UnityContainer`, зарегистрируйте свою службу и установите средство разрешения зависимостей `HttpConfiguration` в новый экземпляр `UnityResolver` для вашего контейнера:

[!code-csharp[](samples/sample9.cs)]

Там, где необходимо, вставьте `IProductRepository`:

[!code-csharp[](samples/sample5.cs)]

Поскольку внедрение зависимостей является частью ASP.NET Core, можно добавить службу в `Startup.ConfigureServices` :

[!code-csharp[](samples/configure-services.cs)]

Репозиторий, как и в Unity, можно внедрять где угодно.

Дополнительные сведения о внедрении зависимостей в ASP.NET Core см. в разделе <xref:fundamentals/dependency-injection> .

## <a name="serving-static-files"></a>Обработка статических файлов

Важной частью разработки веб-приложений является возможность обслуживания статических ресурсов на стороне клиента. Наиболее распространенные примеры статических файлов — это HTML, каскадные таблицы стилей, Javascript и изображения. Эти файлы необходимо сохранять в место публикации приложения (или CDN) и указывать по ссылкам, чтобы они могли загружаться по запросу. В ASP.NET Core ситуация изменилась.

В ASP.NET статические файлы хранятся в разных папках со ссылками в представлениях.

В ASP.NET Core, если не заданы другие настройки, статические файлы хранятся на корневом веб-узле ( *&lt;содержимое корневой папки&gt;/wwwroot* ). Файлы загружаются в конвейер запросов путем вызова метода расширения `UseStaticFiles` из `Startup.Configure`:

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**Примечание** . Для работы с .NET Framework установите пакет NuGet `Microsoft.AspNetCore.StaticFiles`.

Например, ресурс изображения в папке *wwwroot/images* доступен для браузера в расположении `http://<app>/images/<imageFileName>`.

**Примечание.** Более подробное руководство по обслуживанию статических файлов в ASP.NET Core см. в разделе <xref:fundamentals/static-files> .

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Перенос библиотек в .NET Core](/dotnet/core/porting/libraries)
