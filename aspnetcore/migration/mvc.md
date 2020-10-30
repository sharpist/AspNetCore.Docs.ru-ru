---
title: Научитесь переходить с ASP.NET MVC на ASP.NET Core MVC
author: wadepickett
description: Узнайте, как начать миграцию проекта MVC ASP.NET в ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051517"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Миграция с ASP.NET MVC на ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

В этой статье показано, как начать миграцию проекта ASP.NET MVC в [ASP.NET Core MVC](xref:mvc/overview). В процессе он выделяет связанные изменения из ASP.NET MVC.

Миграция с ASP.NET MVC — это многоэтапный процесс. В этой статье рассматриваются следующие вопросы:

* Начальная настройка.
* Основные контроллеры и представления.
* Статическое содержимое.
* Зависимости на стороне клиента.

Сведения о переносе конфигурации и Identity кода см. в разделе [Миграция конфигурации в ASP.NET Core](xref:migration/configuration) и [Миграция проверки подлинности и Identity ASP.NET Core](xref:migration/identity).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Создание проекта MVC для начального ASP.NET

Создайте пример проекта MVC ASP.NET в Visual Studio, чтобы выполнить миграцию:

1. В меню **Файл** выберите пункт **Создать** > **Проект** .
1. Выберите **веб-приложение ASP.NET (.NET Framework)** , а затем нажмите кнопку **Далее** .
1. Присвойте проекту имя *APP1* , чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге. Нажмите кнопку **Создать** .
1. Выберите **MVC** и нажмите кнопку **создать** .

## <a name="create-the-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте новое решение с новым ASP.NET Core проектом, на который будет осуществляться миграция:

1. Запустите второй экземпляр Visual Studio.
1. В меню **Файл** выберите пункт **Создать** > **Проект** .
1. Выберите пункт **Веб-приложение ASP.NET Core** и нажмите кнопку **Далее** .
1. В диалоговом окне **Настройка нового проекта** присвойте проекту имя *APP1* .
1. В качестве расположения укажите каталог, отличный от каталога предыдущего проекта, для использования того же имени проекта. Использование одного и того же пространства имен упрощает копирование кода между двумя проектами. Нажмите кнопку **Создать** .
1. В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1** . Выберите шаблон проекта **веб-приложение (модель-представление-контроллер)** и щелкните **создать** .

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Настройка сайта ASP.NET Core для использования MVC

В проектах ASP.NET Core 3,0 и более поздних версий .NET Framework больше не является поддерживаемой целевой платформой. Проект должен быть предназначен для .NET Core. ASP.NET Core общая платформа, которая включает MVC, является частью установки среды выполнения .NET Core. При использовании пакета SDK `Microsoft.NET.Sdk.Web` в файле проекта автоматически создается ссылка на общую платформу:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Дополнительные сведения см. в разделе [Справочник по платформе](xref:migration/22-to-30#framework-reference).

В ASP.NET Core `Startup` класс:

* Заменяет *Global. asax* .
* Обрабатывает все задачи запуска приложения.

Для получения дополнительной информации см. <xref:fundamentals/startup>.

В проекте ASP.NET Core откройте файл *Startup.CS* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core приложения должны участвовать в функциях платформы с по промежуточного слоя. Предыдущий сформированный шаблоном код добавляет следующие службы и по промежуточного слоя:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Метод расширения регистрирует поддержку служб MVC для контроллеров, функций, связанных с API, и представлений. Дополнительные сведения о параметрах регистрации службы MVC см. в разделе [Регистрация службы MVC](xref:migration/22-to-30#mvc-service-registration) .
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Метод расширения добавляет обработчик статических файлов `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`Метод расширения должен быть вызван раньше `UseRouting` . Для получения дополнительной информации см. <xref:fundamentals/static-files>.
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Метод расширения добавляет маршрутизацию. Для получения дополнительной информации см. <xref:fundamentals/routing>.

Эта существующая конфигурация включает в себя, что необходимо для переноса примера проекта MVC ASP.NET. Дополнительные сведения о параметрах по промежуточного слоя ASP.NET Core см. в разделе <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Миграция контроллеров и представлений

В проекте ASP.NET Core будет добавлен новый пустой класс контроллера и класс представления, которые будут использоваться в качестве заполнителей с теми же именами, что и у контроллера, и классов представлений в любом проекте MVC ASP.NET, из которого выполняется миграция.

Проект ASP.NET Core. *APP1* уже содержит минимальный пример контроллера и представления с тем же именем, что и проект MVC ASP.NET. Таким образом, они будут служить заполнителями для контроллера MVC ASP.NET и представлений, которые будут перенесены *из проекта ASP.NET* MVC.

1. Скопируйте методы из ASP.NET MVC, `HomeController` чтобы заменить новые `HomeController` методы ASP.NET Core. Нет необходимости изменять тип возвращаемого значения методов действия. Тип возвращаемого значения метода действия контроллера встроенного шаблона ASP.NET MVC — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` . Объект `ActionResult` реализует интерфейс `IActionResult`.
1. В проекте ASP.NET Core щелкните правой кнопкой мыши папку *Views/Home* и выберите **Добавить** > **существующий элемент** .
1. В диалоговом окне **Добавление существующего элемента** перейдите к *представлениям/домашнему* каталогу проекта ASP.NET MVC *APP1* .
1. Выберите файлы представлений *About. cshtml* , *Contact. cshtml* и *index. cshtml* Razor , а затем щелкните **Добавить** , заменив существующие файлы.

Дополнительные сведения см. в разделах <xref:mvc/controllers/actions> и <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Тестирование каждого метода

Каждая конечная точка контроллера может быть протестирована, однако макет и стили рассматриваются далее в документе.

1. Запустите приложение ASP.NET Core.
1. Вызовите готовые представления из браузера в работающем ASP.NET Core приложении, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core. Например, `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Миграция статического содержимого

В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера. В ASP.NET Core статические файлы хранятся в [корневом каталоге веб-сайта](xref:fundamentals/index#web-root) проекта. Каталогом по умолчанию является *{Content root}/ввврут* , но его можно изменить. Подробные сведения см. в статье [Статические файлы в ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Скопируйте статическое содержимое из проекта ASP.NET MVC *APP1* в каталог *wwwroot* в *проекте ASP.NET Core.*

1. В проекте ASP.NET Core щелкните правой кнопкой мыши каталог *wwwroot* и выберите **Добавить** > **существующий элемент** .
1. В диалоговом окне **Добавление существующего элемента** перейдите *к проекту ASP.NET* MVC.
1. Выберите файл *фавикон. ico* , а затем нажмите кнопку **Добавить** , заменив существующий файл.

## <a name="migrate-the-layout-files"></a>Перенос файлов макета

Скопируйте файлы макета проекта ASP.NET MVC в проект ASP.NET Core:

1. В проекте ASP.NET Core щелкните правой кнопкой мыши каталог *views* и выберите **Добавить** > **существующий элемент** .
1. В диалоговом окне **Добавление существующего элемента** перейдите к каталогу *views* The *ASP.NET MVC Project* .
1. Выберите файл *_ViewStart. cshtml* и нажмите кнопку **Добавить** .

Скопируйте файлы общего макета проекта ASP.NET MVC в проект ASP.NET Core:

1. В проекте ASP.NET Core щелкните правой кнопкой мыши элемент *представления или общий* каталог и выберите команду **Добавить** > **существующий элемент** .
1. В диалоговом окне **Добавление существующего элемента** перейдите к *представлениям/общему* каталогу проекта ASP.NET MVC *APP1* .
1. Выберите файл *_layout. cshtml* , а затем нажмите кнопку **Добавить** , заменив существующий файл.

В проекте ASP.NET Core откройте файл *_layout. cshtml* . Внесите следующие изменения в соответствии с завершенным кодом, показанным ниже:

Обновите включение начальной загрузки CSS в соответствии с завершенным кодом ниже:

1. Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).
1. Удалите `@Scripts.Render("~/bundles/modernizr")`.

Завершенная замещенная разметка для включения начальной загрузки CSS:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Обновите включение jQuery и начальную загрузку JavaScript в соответствии с завершенным кодом ниже:

1. Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).
1. Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).

Завершенная замещающая разметка для jQuery и начальная загрузка JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Обновленный файл *_layout. cshtml* показан ниже:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Просмотр сайта в браузере. Он должен отображаться с ожидаемыми стилями на месте.

## <a name="configure-bundling-and-minification"></a>Настройка объединения и минификации

ASP.NET Core совместим с несколькими решениями для объединения с открытым кодом и минификации, такими как [оптимизатор](https://github.com/ligershark/WebOptimizer) и другие аналогичные библиотеки. ASP.NET Core не предоставляет собственное решение для объединения и минификации. Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Устранение ошибок HTTP 500

Существует множество проблем, которые могут вызвать сообщение об ошибке HTTP 500, которое не содержит информации об источнике проблемы. Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500. По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке* среды. Это подробно описано в следующем коде:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500. Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере. Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".

## <a name="next-steps"></a>Следующие шаги

* <xref:migration/identity>

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

В этой статье показано, как начать перенос проекта MVC ASP.NET в [ASP.NET Core MVC](xref:mvc/overview) 2,2. В процессе он выделяет многие вещи, которые изменились с ASP.NET MVC. Миграция с ASP.NET MVC — это многоэтапный процесс. В этой статье рассматриваются следующие вопросы:

* Начальная настройка
* Основные контроллеры и представления
* Статическое содержимое
* Зависимости на стороне клиента.

Сведения о переносе конфигурации и Identity кода см. в разделе <xref:migration/configuration> и <xref:migration/identity> .

> [!NOTE]
> Номера версий в примерах могут быть неактуальными. Обновите проекты соответствующим образом.

## <a name="create-the-starter-aspnet-mvc-project"></a>Создание проекта MVC для начального ASP.NET

Чтобы продемонстрировать обновление, мы начнем с создания приложения ASP.NET MVC. Создайте его с именем "имя *APP1* ", чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.

![Диалоговое окно «Создание проекта» Visual Studio](mvc/_static/new-project.png)

![Диалоговое окно "новое веб-приложение": шаблон проекта MVC, выбранный на панели шаблоны ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Необязательно:* Измените имя решения с *Mvc5* *на.* Visual Studio отображает новое имя решения ( *Mvc5* ), которое упрощает указание этого проекта из следующего проекта.

## <a name="create-the-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте *пустое* веб-приложение ASP.NET Core с тем же именем, что и у предыдущего проекта (веб-приложения *APP1* ), чтобы пространства имен в двух проектах совпадали. Наличие одного и того же пространства имен упрощает копирование кода между двумя проектами. Создайте этот проект в каталоге, отличном от каталога предыдущего проекта, для использования того же имени.

![Диалоговое окно "Новый проект"](mvc/_static/new_core.png)

![Диалоговое окно "Создание веб-приложения ASP.NET": пустой шаблон проекта, выбранный на панели "шаблоны ASP.NET Core"](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Необязательно:* Создайте новое приложение ASP.NET Core с помощью шаблона проекта *веб-приложения* . Назовите проект *"* имя_проекта" и выберите параметр проверки подлинности для **отдельных учетных записей пользователей** . Переименуйте это приложение в *фулласпнеткоре* . Создание этого проекта экономит время в преобразовании. Конечный результат можно просмотреть в коде, созданном шаблоном. код можно скопировать в проект преобразования или сравнить с созданным шаблоном проектом.

## <a name="configure-the-site-to-use-mvc"></a>Настройка сайта для использования MVC

* При разработке для .NET Core ссылка на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) используется по умолчанию. Этот пакет содержит пакеты, часто используемые приложениями MVC. Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.

`Microsoft.AspNetCore.Mvc` является ASP.NET Core платформой MVC. `Microsoft.AspNetCore.StaticFiles` является обработчиком статических файлов. ASP.NET Core приложения явно хотят использовать по промежуточного слоя, например для обслуживания статических файлов. Дополнительные сведения см. в разделе [Статические файлы](xref:fundamentals/static-files).

* Откройте файл *Startup.CS* и измените код в соответствии со следующим кодом:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Метод расширения добавляет обработчик статических файлов. Дополнительные сведения см. в разделе Запуск и [Маршрутизация](xref:fundamentals/routing) [приложений](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Добавление контроллера и представления

В этом разделе добавлены минимальный контроллер и представление, которые будут использоваться в качестве заполнителей для контроллера MVC ASP.NET и представлений, перенесенных в следующем разделе.

* Добавьте каталог *Controllers* .

* Добавьте **класс контроллера** с именем *HomeController.CS* в каталог *Controllers* .

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/add_mvc_ctl.png)

* Добавьте каталог *views* .

* Добавление *представлений и домашнего* каталога.

* Добавьте **Razor представление** с именем *index. cshtml* в каталог *Views/Home* .

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/view.png)

Структура проекта показана ниже:

![обозреватель решений отображения файлов и каталогов для файла App1](mvc/_static/project-structure-controller-view.png)

Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:

```html
<h1>Hello world!</h1>
```

Запустите приложение.

![Веб-приложение открыто в Microsoft ребро](mvc/_static/hello-world.png)

Дополнительные сведения см. в разделе [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).

Следующие функции требуют миграции из примера проекта MVC ASP.NET в проект ASP.NET Core:

* содержимое на стороне клиента (CSS, шрифты и скрипты)

* controllers

* узел "Представления"

* модели

* объединения

* filters

* Вход в систему Identity (это делается в следующем руководстве).

## <a name="controllers-and-views"></a>Контроллеры и представления

* Скопируйте каждый из методов из MVC ASP.NET `HomeController` в новый `HomeController` . В ASP.NET MVC тип возвращаемого значения метода действия контроллера встроенного шаблона — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` . `ActionResult` реализует `IActionResult` , поэтому нет необходимости изменять тип возвращаемого значения методов действия.

* Скопируйте файлы представления *About. cshtml* , *Contact. cshtml* и *index. cshtml* Razor из проекта MVC ASP.NET в проект ASP.NET Core.

## <a name="test-each-method"></a>Тестирование каждого метода

Файл макета и стили еще не перенесены, поэтому отображаемые представления содержат только содержимое в файлах представления. Файл макета, в котором созданы ссылки `About` для `Contact` представлений и, пока не будет доступен.

Вызовите готовые представления из браузера в запущенном приложении ASP.NET Core, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core. Например: `https://localhost:44375/home/about`.

![Страница контактов](mvc/_static/contact-page.png)

Обратите внимание на отсутствие стилей и пунктов меню. Стиль будет исправлен в следующем разделе.

## <a name="static-content"></a>Статическое содержимое

В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера. В ASP.NET Core статическое содержимое размещается в каталоге *wwwroot* . Скопируйте статическое содержимое из приложения ASP.NET MVC в каталог *wwwroot* в проекте ASP.NET Core. В этом примере преобразования:

* Скопируйте файл *фавикон. ico* из проекта MVC ASP.NET в каталог *wwwroot* в проекте ASP.NET Core.

Проект ASP.NET MVC использует для своей стилизации [начальную загрузку](https://getbootstrap.com/) и сохраняет файлы начальной загрузки в каталогах *содержимого* и *скриптов* . Шаблон, создавший проект MVC ASP.NET, ссылается на загрузку в файле макета ( *views/Shared/_layout. cshtml* ). Файлы *bootstrap.js* и *начальной загрузки CSS* можно скопировать из проекта MVC ASP.NET в каталог *wwwroot* в новом проекте. Вместо этого в этом документе добавляется поддержка начальной загрузки (и других клиентских библиотек) с помощью CDN в следующем разделе.

## <a name="migrate-the-layout-file"></a>Перенос файла макета

* Скопируйте файл *_ViewStart. cshtml* из каталога *views* проекта ASP.NET MVC в каталог *представлений* проекта ASP.NET Core. Файл *_ViewStart. cshtml* не был изменен в ASP.NET Core MVC.

* Создайте *Общие папки и представления* .

* *Необязательно:* Скопируйте *_ViewImports. cshtml* из каталога *views* проекта *фулласпнеткоре* MVC в каталог *представлений* проекта ASP.NET Core. Удалите любое объявление пространства имен в файле *_ViewImports. cshtml* . Файл *_ViewImports. cshtml* предоставляет пространства имен для всех файлов представлений и переводит их в [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro). Вспомогательные функции тегов используются в новом файле макета. Файл *_ViewImports. cshtml* является новым для ASP.NET Core.

* Скопируйте файл *_layout. cshtml* из *представлений и общих* каталогов проекта MVC ASP.NET в *представления и общий* каталог проекта ASP.NET Core.

Откройте файл *_layout. cshtml* и внесите следующие изменения (завершенный код показан ниже):

* Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).

* Удалите `@Scripts.Render("~/bundles/modernizr")`.

* Закомментируйте `@Html.Partial("_LoginPartial")` строку (заключите строку в `@*...*@` ). Дополнительные сведения см. [в разделе Миграция проверки подлинности и Identity в ASP.NET Core](xref:migration/identity)

* Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).

* Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).

Заменяющая разметка для включения начальной загрузки CSS:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Заменяющая разметка для jQuery и начальная загрузка JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Обновленный файл *_layout. cshtml* показан ниже:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Просмотр сайта в браузере. Теперь она должна быть правильно загружена с ожидаемыми стилями.

* *Необязательно:* Попробуйте использовать новый файл макета. Скопируйте файл макета из проекта *фулласпнеткоре* . Новый файл макета использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и содержит другие улучшения.

## <a name="configure-bundling-and-minification"></a>Настройка объединения и минификации

Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Устранение ошибок HTTP 500

Существует множество проблем, которые могут вызвать сообщения об ошибках HTTP 500, не содержащие сведений о источнике проблемы. Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500. По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке* конфигурации. См. пример в следующем коде:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500. Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере. Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

В этой статье показано, как начать перенос проекта MVC ASP.NET в [ASP.NET Core MVC](xref:mvc/overview) 2,1. В процессе он выделяет многие вещи, которые изменились с ASP.NET MVC. Миграция с ASP.NET MVC — это многоэтапный процесс. В этой статье рассматриваются следующие вопросы:

* Начальная настройка
* Основные контроллеры и представления
* Статическое содержимое
* Зависимости на стороне клиента.

Сведения о переносе конфигурации и Identity кода см. в разделе [Миграция конфигурации в ASP.NET Core](xref:migration/configuration) и [Миграция проверки подлинности и Identity ASP.NET Core](xref:migration/identity).

> [!NOTE]
> Номера версий в примерах могут быть неактуальными. Обновите проекты соответствующим образом.

## <a name="create-the-starter-aspnet-mvc-project"></a>Создание проекта MVC для начального ASP.NET

Чтобы продемонстрировать обновление, мы начнем с создания приложения ASP.NET MVC. Создайте его с именем "имя *APP1* ", чтобы пространство имен совпадало с ASP.NET Core проектом, созданным на следующем шаге.

![Диалоговое окно «Создание проекта» Visual Studio](mvc/_static/new-project.png)

![Диалоговое окно "новое веб-приложение": шаблон проекта MVC, выбранный на панели шаблоны ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Необязательно:* Измените имя решения с *Mvc5* *на.* Visual Studio отображает новое имя решения ( *Mvc5* ), которое упрощает указание этого проекта из следующего проекта.

## <a name="create-the-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте *пустое* веб-приложение ASP.NET Core с тем же именем, что и у предыдущего проекта (веб-приложения *APP1* ), чтобы пространства имен в двух проектах совпадали. Наличие одного и того же пространства имен упрощает копирование кода между двумя проектами. Создайте этот проект в каталоге, отличном от каталога предыдущего проекта, для использования того же имени.

![Диалоговое окно "Новый проект"](mvc/_static/new_core.png)

![Диалоговое окно "Создание веб-приложения ASP.NET": пустой шаблон проекта, выбранный на панели "шаблоны ASP.NET Core"](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Необязательно:* Создайте новое приложение ASP.NET Core с помощью шаблона проекта *веб-приложения* . Назовите проект *"* имя_проекта" и выберите параметр проверки подлинности для **отдельных учетных записей пользователей** . Переименуйте это приложение в *фулласпнеткоре* . Создание этого проекта экономит время в преобразовании. Конечный результат можно просмотреть в коде, созданном шаблоном. код можно скопировать в проект преобразования или сравнить с созданным шаблоном проектом.

## <a name="configure-the-site-to-use-mvc"></a>Настройка сайта для использования MVC

* При разработке для .NET Core ссылка на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) используется по умолчанию. Этот пакет содержит пакеты, часто используемые приложениями MVC. Если целевой объект .NET Framework, ссылки на пакеты должны быть перечислены по отдельности в файле проекта.

`Microsoft.AspNetCore.Mvc` является ASP.NET Core платформой MVC. `Microsoft.AspNetCore.StaticFiles` является обработчиком статических файлов. ASP.NET Core приложения явно хотят использовать по промежуточного слоя, например для обслуживания статических файлов. Дополнительные сведения см. в разделе [Статические файлы](xref:fundamentals/static-files).

* Откройте файл *Startup.CS* и измените код в соответствии со следующим кодом:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Метод расширения добавляет обработчик статических файлов. `UseMvc`Метод расширения добавляет маршрутизацию. Дополнительные сведения см. в разделе Запуск и [Маршрутизация](xref:fundamentals/routing) [приложений](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Добавление контроллера и представления

В этом разделе добавлены минимальный контроллер и представление, которые будут использоваться в качестве заполнителей для контроллера MVC ASP.NET и представлений, перенесенных в следующем разделе.

* Добавьте каталог *Controllers* .

* Добавьте **класс контроллера** с именем *HomeController.CS* в каталог *Controllers* .

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/add_mvc_ctl.png)

* Добавьте каталог *views* .

* Добавление *представлений и домашнего* каталога.

* Добавьте **Razor представление** с именем *index. cshtml* в каталог *Views/Home* .

![Диалоговое окно ''Добавление нового элемента''](mvc/_static/view.png)

Структура проекта показана ниже:

![обозреватель решений отображения файлов и каталогов для файла App1](mvc/_static/project-structure-controller-view.png)

Замените содержимое файла *Views/Home/Index.cshtml* следующей разметкой:

```html
<h1>Hello world!</h1>
```

Запустите приложение.

![Веб-приложение открыто в Microsoft ребро](mvc/_static/hello-world.png)

Дополнительные сведения см. в разделе [Controllers](xref:mvc/controllers/actions) and [views](xref:mvc/views/overview).

Следующие функции требуют миграции из примера проекта MVC ASP.NET в проект ASP.NET Core:

* содержимое на стороне клиента (CSS, шрифты и скрипты)

* controllers

* узел "Представления"

* модели

* объединения

* filters

* Вход в систему Identity (это делается в следующем руководстве).

## <a name="controllers-and-views"></a>Контроллеры и представления

* Скопируйте каждый из методов из MVC ASP.NET `HomeController` в новый `HomeController` . В ASP.NET MVC тип возвращаемого значения метода действия контроллера встроенного шаблона — <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; в ASP.NET Core MVC вместо этого методы действия возвращают `IActionResult` . `ActionResult` реализует `IActionResult` , поэтому нет необходимости изменять тип возвращаемого значения методов действия.

* Скопируйте файлы представления *About. cshtml* , *Contact. cshtml* и *index. cshtml* Razor из проекта MVC ASP.NET в проект ASP.NET Core.

## <a name="test-each-method"></a>Тестирование каждого метода

Файл макета и стили еще не перенесены, поэтому отображаемые представления содержат только содержимое в файлах представления. Файл макета, в котором созданы ссылки `About` для `Contact` представлений и, пока не будет доступен.

* Вызовите готовые представления из браузера в запущенном приложении ASP.NET Core, заменив текущий номер порта на номер порта, используемый в проекте ASP.NET Core. Например: `https://localhost:44375/home/about`.

![Страница контактов](mvc/_static/contact-page.png)

Обратите внимание на отсутствие стилей и пунктов меню. Стиль будет исправлен в следующем разделе.

## <a name="static-content"></a>Статическое содержимое

В ASP.NET MVC 5 и более ранних версиях статическое содержимое было размещено из корневого каталога веб-проекта и было смешано с файлами на стороне сервера. В ASP.NET Core статическое содержимое размещается в каталоге *wwwroot* . Скопируйте статическое содержимое из приложения ASP.NET MVC в каталог *wwwroot* в проекте ASP.NET Core. В этом примере преобразования:

* Скопируйте файл *фавикон. ico* из проекта MVC ASP.NET в каталог *wwwroot* в проекте ASP.NET Core.

Проект ASP.NET MVC использует для своей стилизации [начальную загрузку](https://getbootstrap.com/) и сохраняет файлы начальной загрузки в каталогах *содержимого* и *скриптов* . Шаблон, создавший проект MVC ASP.NET, ссылается на загрузку в файле макета ( *views/Shared/_layout. cshtml* ). Файлы *bootstrap.js* и *начальной загрузки CSS* можно скопировать из проекта MVC ASP.NET в каталог *wwwroot* в новом проекте. Вместо этого в этом документе добавляется поддержка начальной загрузки (и других клиентских библиотек) с помощью CDN в следующем разделе.

## <a name="migrate-the-layout-file"></a>Перенос файла макета

* Скопируйте файл *_ViewStart. cshtml* из каталога *views* проекта ASP.NET MVC в каталог *представлений* проекта ASP.NET Core. Файл *_ViewStart. cshtml* не был изменен в ASP.NET Core MVC.

* Создайте *Общие папки и представления* .

* *Необязательно:* Скопируйте *_ViewImports. cshtml* из каталога *views* проекта *фулласпнеткоре* MVC в каталог *представлений* проекта ASP.NET Core. Удалите любое объявление пространства имен в файле *_ViewImports. cshtml* . Файл *_ViewImports. cshtml* предоставляет пространства имен для всех файлов представлений и переводит их в [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro). Вспомогательные функции тегов используются в новом файле макета. Файл *_ViewImports. cshtml* является новым для ASP.NET Core.

* Скопируйте файл *_layout. cshtml* из *представлений и общих* каталогов проекта MVC ASP.NET в *представления и общий* каталог проекта ASP.NET Core.

Откройте файл *_layout. cshtml* и внесите следующие изменения (завершенный код показан ниже):

* Замените на `@Styles.Render("~/Content/css")` `<link>` элемент для загрузки *начальной загрузки. CSS* (см. ниже).

* Удалите `@Scripts.Render("~/bundles/modernizr")`.

* Закомментируйте `@Html.Partial("_LoginPartial")` строку (заключите строку в `@*...*@` ). Дополнительные сведения см. [в разделе Миграция проверки подлинности и Identity в ASP.NET Core](xref:migration/identity)

* Замените на `@Scripts.Render("~/bundles/jquery")` `<script>` элемент (см. ниже).

* Замените на `@Scripts.Render("~/bundles/bootstrap")` `<script>` элемент (см. ниже).

Заменяющая разметка для включения начальной загрузки CSS:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Заменяющая разметка для jQuery и начальная загрузка JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Обновленный файл *_layout. cshtml* показан ниже:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Просмотр сайта в браузере. Теперь она должна быть правильно загружена с ожидаемыми стилями.

* *Необязательно:* Попробуйте использовать новый файл макета. Скопируйте файл макета из проекта *фулласпнеткоре* . Новый файл макета использует [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) и содержит другие улучшения.

## <a name="configure-bundling-and-minification"></a>Настройка объединения и минификации

Сведения о настройке объединения и минификации см. в разделе [объединение и минификации](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Устранение ошибок HTTP 500

Существует множество проблем, которые могут вызвать сообщения об ошибках HTTP 500, не содержащие сведений о источнике проблемы. Например, если файл *views/_ViewImports. cshtml* содержит пространство имен, которое не существует в проекте, создается ошибка HTTP 500. По умолчанию в ASP.NET Core приложениях `UseDeveloperExceptionPage` расширение добавляется в `IApplicationBuilder` и выполняется при *разработке* конфигурации. См. пример в следующем коде:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core преобразует необработанные исключения в отклики об ошибках HTTP 500. Как правило, сведения об ошибке не включаются в эти ответы, чтобы предотвратить раскрытие потенциально конфиденциальной информации о сервере. Дополнительные сведения см. в разделе [Страница "исключение разработчика](xref:fundamentals/error-handling#developer-exception-page)".

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
