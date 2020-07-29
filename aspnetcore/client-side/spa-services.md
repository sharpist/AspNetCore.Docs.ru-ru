---
title: Создание одностраничных приложений в ASP.NET Core с помощью служб JavaScript
author: scottaddie
description: Ознакомьтесь с преимуществами использования служб JavaScript для создания одностраничных приложений на основе ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: client-side/spa-services
ms.openlocfilehash: 05f76a7d341fc5c55b8234b6ff6d2be5aa61d6fd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401835"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Создание одностраничных приложений в ASP.NET Core с помощью служб JavaScript

Авторы: [Скотт Адди](https://github.com/scottaddie) (Scott Addie) и [Фияз Хасан](https://fiyazhasan.me/) (Fiyaz Hasan)

Одностраничные приложения (SPA) — это популярный тип веб-приложений из-за присущих им широких возможностей пользовательского интерфейса. Интеграция клиентских платформ и библиотек SPA, таких как [Angular](https://angular.io/) или [React](https://facebook.github.io/react/), с серверными платформами, такими как ASP.NET Core, может представлять сложность. Чтобы упростить интеграцию, были разработаны службы JavaScript. Они обеспечивают эффективное взаимодействие между различными стеками клиентских и серверных технологий.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Функции, описанные в этой статье, устарели с версии ASP.NET Core 3.0. В пакете NuGet [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) реализован более простой механизм интеграции с платформами SPA. Дополнительные сведения см. в [объявлении об устаревании Microsoft.AspNetCore.SpaServices и Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Что такое службы JavaScript

Службы JavaScript — это набор технологий на стороне клиента для ASP.NET Core. Его целью является позиционирование ASP.NET Core в качестве предпочтительной серверной платформы для разработки одностраничных приложений.

В состав служб JavaScript входят два отдельных пакета NuGet:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Эти пакеты полезны в следующих случаях:

* выполнение JavaScript на сервере;
* использование платформы или библиотеки SPA;
* создание ресурсов на стороне клиента с помощью Webpack.

В этой статье основное внимание уделяется использованию пакета SpaServices.

## <a name="what-is-spaservices"></a>Что такое SpaServices

Пакет SpaServices был создан с целью позиционирования ASP.NET Core в качестве предпочтительной серверной платформы для разработки одностраничных приложений. Он не обязателен для разработки одностраничных приложений с помощью ASP.NET Core и не ограничивает выбор разработчиков определенной клиентской платформой.

Пакет SpaServices предоставляет полезные инфраструктурные возможности, в том числе:

* [предварительная обработка на стороне сервера](#server-side-prerendering);
* [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware);
* [горячая замена модулей](#hot-module-replacement);
* [вспомогательные методы маршрутизации](#routing-helpers).

В совокупности эти компоненты инфраструктуры улучшают как процесс разработки, так и выполнение приложений. Их можно внедрять по отдельности.

## <a name="prerequisites-for-using-spaservices"></a>Необходимые условия для использования SpaServices

Для работы со SpaServices установите перечисленные ниже компоненты.

* [Node.js](https://nodejs.org/) (версии 6 или более поздней) с npm

  * Чтобы проверить, установлены ли эти компоненты, выполните в командной строке следующую команду:

    ```console
    node -v && npm -v
    ```

  * При развертывании на веб-сайте Azure никаких действий не требуется &mdash; платформа Node.js уже установлена и доступна в серверных средах.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * В ОС Windows с Visual Studio 2017 для установки пакета SDK нужно выбрать рабочую нагрузку **Кроссплатформенная разработка .NET Core**.

* Пакет NuGet [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Предварительная обработка на стороне сервера

Универсальное приложение (также называемое изоморфным) — это приложение JavaScript, которое может выполняться как на сервере, так и в клиенте. Angular, React и другие популярные платформы обеспечивают возможности для разработки приложений в таком стиле. Главный принцип заключается в том, что компоненты платформы сначала обрабатываются на сервере с помощью Node.js, после чего передаются в клиент для дальнейшего выполнения.

[Вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) ASP.NET Core, предоставляемые пакетом SpaServices, упрощают реализацию предварительной обработки на стороне сервера путем вызова функций JavaScript на сервере.

### <a name="server-side-prerendering-prerequisites"></a>Необходимые условия для предварительной обработки на стороне сервера

Установите пакет npm [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Настройка предварительной обработки на стороне сервера

Вспомогательные функции тегов становятся доступны путем регистрации пространства имен в файле *_ViewImports.cshtml* проекта:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Эти функции позволяют абстрагироваться от сложностей, связанных с взаимодействием с низкоуровневыми интерфейсами API напрямую, благодаря использованию синтаксиса в стиле HTML в представлении [Razor:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Вспомогательная функция тегов asp-prerender-module

Вспомогательная функция тегов `asp-prerender-module`, используемая в предыдущем примере кода, выполняет файл *ClientApp/dist/main-server.js* на сервере с помощью Node.js. Чтобы было понятно, уточним, что файл *main-server.js* — это артефакт, получаемый в результате выполнения задачи транспилирования TypeScript в JavaScript в процессе сборки [Webpack](https://webpack.github.io/). В Webpack определен псевдоним точки входа `main-server`. Обход схемы зависимостей для этого псевдонима начинается с файла *ClientApp/boot-server.ts*:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

В следующем примере для Angular файл *ClientApp/boot-server.ts* использует функцию `createServerRenderer` и тип `RenderResult` из пакета npm `aspnet-prerendering` для настройки обработки на сервере с помощью Node.js. Разметка HTML, предназначенная для обработки на стороне сервера, передается в вызов функции resolve, который заключен в строго типизированный объект JavaScript `Promise`. Важность объекта `Promise` заключается в том, что он асинхронно передает HTML-разметку странице для внедрения в элемент-заполнитель модели DOM.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>Вспомогательная функция тегов asp-prerender-data

При использовании в сочетании с функцией `asp-prerender-module` вспомогательную функцию тегов `asp-prerender-data` можно использовать для передачи контекстных сведений из представления [Razor в код JavaScript на стороне сервера. Например, следующая разметка передает пользовательские данные в модуль `main-server`:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Полученный аргумент `UserName` сериализуется с помощью встроенного сериализатора JSON и сохраняется в объекте `params.data`. В следующем примере для Angular данные используются для создания персонализированного приветствия в элементе `h1`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Имена свойств, передаваемые во вспомогательных функциях тегов, представлены в нотации **РегистрПаскаля**. В отличие от этого, в JavaScript те же самые имена свойств представлены в **верблюжьем регистре**. Это различие обуславливается конфигурацией сериализации JSON по умолчанию.

Если продолжить предыдущий пример кода, данные можно передать с сервера в представление путем расконсервации свойства `globals`, предоставляемого функции `resolve`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Массив `postList`, определенный внутри объекта `globals`, присоединяется к глобальному объекту `window` браузера. Перевод переменной в глобальную область позволяет избежать лишних усилий, особенно когда одни и те же данные загружаются сначала на сервере, а затем в клиенте.

![глобальная переменная postList, присоединенная к объекту window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>ПО промежуточного слоя Webpack для разработки

[ПО промежуточного слоя Webpack для разработки](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) упрощает процесс разработки благодаря сборке ресурсов средством Webpack по запросу. Оно автоматически компилирует и предоставляет ресурсы на стороне клиента при перезагрузке страницы в браузере. Альтернативный подход заключается в вызове Webpack вручную через скрипт сборки npm проекта при изменении сторонней зависимости или пользовательского кода. Скрипт сборки npm в файле *package.json* представлен в следующем примере:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Необходимые условия для использования ПО промежуточного слоя Webpack для разработки

Установите пакет npm [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Настройка ПО промежуточного слоя Webpack для разработки

ПО промежуточного слоя Webpack для разработки регистрируется в конвейере HTTP-запросов с помощью следующего кода в методе `Configure` в файле *Startup.cs*:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Метод расширения `UseWebpackDevMiddleware` должен вызываться перед [регистрацией размещения статического файла](xref:fundamentals/static-files) с помощью метода расширения `UseStaticFiles`. В целях безопасности регистрировать ПО промежуточного слоя следует только при запуске приложения в режиме разработки.

Свойство `output.publicPath` в файле *webpack.config.js* предписывает ПО промежуточного слоя отслеживать изменения в папке `dist`:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Горячая замена модулей

Функцию [горячей замены модулей](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) в Webpack можно рассматривать как дальнейшее развитие [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware). HMR дает те же преимущества, но еще более упрощает процесс разработки благодаря автоматическому обновлению содержимого страницы после компиляции изменений. Не путайте это с обновлением содержимого браузера, которое нарушает текущее состояние в памяти и сеанс отладки приложения SPA. Между службой ПО промежуточного слоя Webpack для разработки и браузером существует динамическая связь, благодаря которой изменения передаются в браузер.

### <a name="hot-module-replacement-prerequisites"></a>Необходимые условия для горячей замены модулей

Установите пакет npm [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Настройка горячей замены модулей

Компонент HMR необходимо зарегистрировать в конвейере HTTP-запросов MVC в методе `Configure`:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Так же как и в случае с [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware), метод расширения `UseWebpackDevMiddleware` должен вызываться до метода расширения `UseStaticFiles`. В целях безопасности регистрировать ПО промежуточного слоя следует только при запуске приложения в режиме разработки.

В файле *webpack.config.js* должен определяться массив `plugins`, даже если он будет пустым:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

После загрузки приложения в браузере на вкладке "Консоль" средств разработчика выводится подтверждение активации HMR.

![Сообщение о подключении функции горячей замены модулей](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Вспомогательные методы маршрутизации

В большинстве одностраничных приложений на основе ASP.NET Core, помимо маршрутизации на стороне сервера, часто требуется маршрутизация на стороне клиента. Системы маршрутизации SPA и MVC могут работать независимо, не мешая друг другу. Однако существует один пограничный случай, вызывающий трудности: идентификация HTTP-ответов 404.

Рассмотрим ситуацию, в которой используется маршрут `/some/page` без расширений. Предположим, что шаблон запроса не соответствует маршруту на стороне сервера, но соответствует маршруту на стороне клиента. Теперь предположим, что поступил запрос пути `/images/user-512.png`. Такой запрос обычно служит для поиска файла изображения на сервере. Если запрошенный путь к ресурсу не соответствует ни одному маршруту на стороне сервера или статическому файлу, то маловероятно, что клиентское приложение будет его обрабатывать &mdash; обычно в таких случаях возвращается код состояния HTTP 404.

### <a name="routing-helpers-prerequisites"></a>Необходимые условия для использования вспомогательных методов маршрутизации

Установите пакет npm для маршрутизации на стороне клиента. Вот пример для Angular:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Настройка вспомогательных методов маршрутизации

В методе `Configure` используется метод расширения `MapSpaFallbackRoute`:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Маршруты оцениваются в той очередности, в которой они были настроены. Поэтому в предыдущем примере кода для сопоставления шаблонов сначала используется маршрут `default`.

## <a name="create-a-new-project"></a>Создание нового проекта

Службы JavaScript предоставляют предварительно настроенные шаблоны приложений. Пакет SpaServices используется в них в сочетании с различными платформами и библиотеками, таким как Angular, React и Redux.

Эти шаблоны можно установить с помощью .NET Core CLI, выполнив следующую команду:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Отобразится список доступных шаблонов SPA.

| Шаблоны                                 | Краткое имя | Язык | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core с Angular             | angular    | [C#]     | MVC/Веб/SPA |
| MVC ASP.NET Core с React.js            | react      | [C#]     | MVC/Веб/SPA |
| MVC ASP.NET Core с React.js и Redux  | reactredux | [C#]     | MVC/Веб/SPA |

Чтобы создать проект с помощью одного из шаблонов SPA, включите **короткое имя** шаблона в команду [dotnet new](/dotnet/core/tools/dotnet-new). Следующая команда создает приложение Angular с MVC ASP.NET Core, настроенное как серверное:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Задание режима конфигурации среды выполнения

Существует два основных режима конфигурации среды выполнения:

* **Разработка**:
  * включает сопоставители с исходным кодом для упрощения отладки;
  * не оптимизирует производительность кода на стороне клиента.
* **Рабочая среда**:
  * исключает сопоставители с исходным кодом;
  * оптимизирует код на стороне клиента посредством объединения и минификации.

В ASP.NET Core режим конфигурации хранится в переменной среды `ASPNETCORE_ENVIRONMENT`. Дополнительные сведения см. в разделе [Указание среды](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Запуск с помощью .NET Core CLI

Восстановите необходимые пакеты NuGet и npm, выполнив следующую команду в корневом каталоге проекта:

```dotnetcli
dotnet restore && npm i
```

Выполните сборку и запуск приложения:

```dotnetcli
dotnet run
```

Приложение запускается на узле localhost в соответствии с [режимом конфигурации среды выполнения](#set-the-runtime-configuration-mode). При переходе по адресу `http://localhost:5000` в браузере открывается целевая страница.

### <a name="run-with-visual-studio-2017"></a>Запуск с помощью Visual Studio 2017

Откройте файл *CSPROJ*, созданный с помощью команды [dotnet new](/dotnet/core/tools/dotnet-new). Необходимые пакеты NuGet и npm восстанавливаются автоматически при открытии проекта: Процесс восстановления может занять несколько минут. По его завершении приложение будет готово к запуску. Нажмите зеленую кнопку запуска или клавиши `Ctrl + F5`, и в браузере откроется целевая страница приложения. Приложение запускается на узле localhost в соответствии с [режимом конфигурации среды выполнения](#set-the-runtime-configuration-mode).

## <a name="test-the-app"></a>Тестирование приложения

В шаблонах SpaServices предварительно настроено выполнение тестов на стороне клиента с помощью [Karma](https://karma-runner.github.io/1.0/index.html) и [Jasmine](https://jasmine.github.io/). Jasmine — это популярная платформа модульного тестирования для JavaScript, а Karma — это средство запуска тестов. Средство Karma настроено для работы с [ПО промежуточного слоя Webpack для разработки](#webpack-dev-middleware), так что разработчику не нужно останавливать и снова запускать тест каждый раз, когда вносится изменение. Независимо от того, выполняется ли сам тестовый случай или код для тестового случая, тест проводится автоматически.

Возьмем в качестве примера приложение Angular. В файле *counter.component.spec.ts* уже имеется два тестовых случая Jasmine для `CounterComponent`:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Откройте командную строку в каталоге *ClientApp*. Выполните следующую команду:

```console
npm test
```

Скрипт запускает средство Karma, которое считывает параметры, определенные в файле *karma.conf.js*. Помимо прочих параметров, в файле *karma.conf.js* определены тестовые файлы, которые должны выполняться для массива `files`:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Публикация приложения

Дополнительные сведения о публикации в Azure см. в [описании этой проблемы в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474).

Объединение созданных клиентских ресурсов и опубликованных артефактов ASP.NET Core в пакет, готовый к развертыванию, может быть непростой задачей. К счастью, пакет SpaServices координирует весь процесс публикации с помощью настраиваемого целевого объекта MSBuild с именем `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Целевой объект MSBuild отвечает за следующие задачи:

1. восстановление пакетов npm;
1. создание сборки сторонних клиентских ресурсов для рабочей среды;
1. создание сборки пользовательских клиентских ресурсов для рабочей среды;
1. копирование ресурсов, созданных средством Webpack, в папку публикации.

Целевой объект MSBuild вызывается при выполнении следующей команды:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация Angular](https://angular.io/docs)
