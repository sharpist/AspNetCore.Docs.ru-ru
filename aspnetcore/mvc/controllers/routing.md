---
title: Маршрутизация к действиям контроллера в ASP.NET Core
author: rick-anderson
description: Узнайте, как в MVC ASP.NET Core используется ПО промежуточного слоя маршрутизации для сопоставления URL-адресов входящих запросов с действиями.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 74afd0a076ca8bd753000f547ef0a26308e8a884
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123489"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Маршрутизация к действиям контроллера в ASP.NET Core

[Райан Новак](https://github.com/rynowak), [Кирк Ларкин](https://twitter.com/serpent5), и [Рик Андерсон](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET контроллеры Core используют [промежуточное программное обеспечение](xref:fundamentals/middleware/index) Routing для сопоставления URL-адресов входящих запросов и сопометки их к [действиям.](#action)  Шаблоны маршрутов:

* Определены в коде запуска или атрибутах.
* Опишите, как пути URL сопоставляются с [действиями.](#action)
* Используются для создания URL-адресов для ссылок. Генерируемые ссылки обычно возвращаются в ответах.

Действия либо [условно-маршрутизируются,](#cr) либо [направляются атрибутами.](#ar) Размещение маршрута на контроллере или [действии](#action) делает его атрибут-маршрутизатором. Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).

Этот документ:

* Объясняет взаимодействие между MVC и routing:
  * Как типичные приложения MVC используют функции конечной вымысания.
  * Покрывает оба:
    * [Условно йаутинг](#cr) обычно используется с контроллерами и представлениями.
    * *Атрибутная направление* используется с помощью aIS REST. Если вы в первую очередь заинтересованы в области routing для REST AIS, перейти к [атрибуту трассировки для раздела REST AIS.](#ar)
  * Посмотреть [Routing](xref:fundamentals/routing) для расширенных деталей разгрома.
* Относится к системе по умолчанию, добавленной в ASP.NET Core 3.0, называемой конечным пунктом. Можно использовать контроллеры с предыдущей версией направления для целей совместимости. Для получения инструкций смотрите [руководство по миграции 2.2-3.0.](xref:migration/22-to-30) Ссылайтесь на [версию 2.2 этого документа](xref:mvc/controllers/routing?view=aspnetcore-2.2) для справочного материала по устаревшей системе конечной выдвижения.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Настройка обычного маршрута

`Startup.Configure`обычно имеет код, похожий на следующий при [использовании обычной реукторе:](#crd)

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Внутри <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>вызова, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> используется для создания единого маршрута. Единый маршрут `default` называется маршрут. Большинство приложений с контроллерами и представлениями `default` используют шаблон маршрута, похожий на маршрут. REST AIS должны использовать [атрибуты routing.](#ar)

Шаблон `"{controller=Home}/{action=Index}/{id?}"`маршрута :

* Соответствует траектории URL-`/Products/Details/5`
* Извлекает значения `{ controller = Products, action = Details, id = 5 }` маршрута, токенизазовав путь. Извлечение значений маршрута приводит к совпадению, если приложение имеет имя `ProductsController` контроллера `Details` и действие:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`модель связывает значение `id = 5` для установки `id` `5`параметра. Подробнее о [см.](xref:mvc/models/model-binding)
* `{controller=Home}`определяет `Home` как значение `controller`по умолчанию.
* `{action=Index}`определяет `Index` как значение `action`по умолчанию.
*  Символ `?` в `{id?}` определяет `id` как необязательный.
  * Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления. Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).
* Соответствует траектории `/`URL.
* Производит значения `{ controller = Home, action = Index }`маршрута.

Значения для `controller` значений по умолчанию используются и `action` используются. `id`не производит значение, так как в пути URL нет соответствующего сегмента. `/`только совпадает, `HomeController` если `Index` существует действие:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Используя предыдущее определение контроллера `HomeController.Index` и шаблон маршрута, действие выполняется для следующих путей URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Путь `/` URL использует контроллеры `Home` и `Index` действия шаблона маршрута по умолчанию. Путь `/Home` URL использует действие `Index` шаблона маршрута по умолчанию.

Универсальный метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Заменяет:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Routing настроен с <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> помощью и <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> промежуточного посуды. Для использования контроллеров:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> Звоните `UseEndpoints` внутрь, чтобы [отобрадить приписываемые](#ar) контроллеры.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Позвоните <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>или , чтобы составить карту [условно маршрутистирных](#cr) контроллеров.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Маршрутизация на основе соглашений

Обычная реуктора используется с контроллерами и представлениями. Маршрут `default`:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

является примером *маршрутизации на основе соглашений*. Это называется *обычной маршрутизирующей сяре,* поскольку она устанавливает *конвенцию* для путей URL:

* Первый сегмент `{controller=Home}`пути, карты к имени контроллера.
* Второй сегмент, `{action=Index}`карты к названию [действия.](#action)
* Третий сегмент, `{id?}` используется для `id`факультативного . `?` В `{id?}` делает его необязательным. `id`используется для картирования модели сущности.

Используя `default` этот маршрут, путь URL:

* `/Products/List`карты к `ProductsController.List` действию.
* `/Blog/Article/17`карты `BlogController.Article` и, как правило, модель связывает `id` параметр с 17.

Это отображение:

* Основано только на **контроллере**и именах [действий.](#action)
* Не основано на пространствах имен, местоположении исходного файла или параметрах метода.

Использование обычной маршрутики с маршрутом по умолчанию позволяет создать приложение без необходимости придумывать новый шаблон URL для каждого действия. Для приложения с действиями стиля [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) имеющего согласованность для URL-адресов между контроллерами:

* Помогает упростить код.
* Делает uI более предсказуемым.

> [!WARNING]
> В `id` предыдущем коде определяется как необязательный шаблоном маршрута. Действия могут выполняться без дополнительного идентификатора, предоставленного как часть URL-. Как правило, при`id` исключении из URL-адреса:
>
> * `id`устанавливается `0` по привязке модели.
> * В сопоставлении `id == 0`базы данных не найдено сущность.
>
> [Ацентная routing](#ar) обеспечивает мелкозернистый контроль, чтобы сделать идентификатор, необходимый для некоторых действий, а не для других. По конвенции документация включает `id` в себя дополнительные параметры, например, когда они могут отображаться при правильном использовании.

Для большинства приложений следует выбрать базовую описательную схему маршрутизации таким образом, чтобы URL-адреса были удобочитаемыми и осмысленными. Традиционный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`.

* Поддерживает основную и описательную схемы маршрутизации.
* Является отправной точкой для приложений на базе пользовательского интерфейса.
* Является единственным шаблоном маршрута, необходимым для многих приложений веб-uI. Для более крупных приложений веб-uI, другой маршрут с использованием [области,](#areas) если часто все, что нужно.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>и: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>

* Автоматически присваивай значение **заказа** конечным точкам в зависимости от заказа, на который они ссылаются.

Конечная точка в ASP.NET Core 3.0 и позже:

* Не имеет понятия маршрутов.
* Не дает гарантий заказа на выполнение расширяемости, все конечные точки обрабатываются сразу.

Чтобы увидеть, как встроенные реализации маршрутизации, такие как <xref:Microsoft.AspNetCore.Routing.Route>, сопоставляются с запросами, включите [ведение журнала](xref:fundamentals/logging/index).

В этом документе [позже](#ar) объясняется разгром атрибута.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Несколько обычных маршрутов

Несколько [обычных маршрутов](#cr) могут быть добавлены внутри, `UseEndpoints` добавив больше звонков <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. Это позволяет определить несколько конвенций или добавить обычные маршруты, предназначенные для конкретного [действия,](#action)такие как:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Маршрут `blog` в предыдущем коде представляет собой **выделенный обычный маршрут.** Это называется выделенным обычным маршрутом, потому что:

* Он использует [обычные области.](#cr)
* Он посвящен конкретному [действию.](#action)

Потому что `controller` и `action` не отображаются в шаблоне `"blog/{*article}"` маршрута в качестве параметров:

* Они могут иметь только `{ controller = "Blog", action = "Article" }`значения по умолчанию.
* Этот маршрут всегда отображает `BlogController.Article`сява к действию.

`/Blog`, `/Blog/Article`и `/Blog/{any-string}` являются единственными url-маршрутами, которые соответствуют маршруту блога.

Предыдущий пример:

* `blog`маршрут имеет более высокий `default` приоритет для совпадений, чем маршрут, потому что он добавляется первым.
* Является и примером [slug](https://developer.mozilla.org/docs/Glossary/Slug) стиль разгрома, где это типично иметь имя статьи как часть URL.

> [!WARNING]
> В ASP.NET Core 3.0 и позже, направление не:
> * Определите концепцию, называемую *маршрутом.* `UseRouting`добавляет маршрут, соответствующий конвейеру промежуточного посуды. Промежуточное `UseRouting` программное обеспечение рассматривает набор конечных точек, определенных в приложении, и выбирает наилучший матч конечной точки на основе запроса.
> * Предоставьте гарантии о порядке исполнения <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> расширяемости как или <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.
>
>Смотрите [Routing](xref:fundamentals/routing) для справочного материала по реудалировке.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Обычный порядок вхомливания

Обычная routing только соответствует комбинации действий и контроллера, которые определяются приложением. Это призвано упростить случаи перекрытия обычных маршрутов.
Добавление маршрутов <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>с <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> использованием, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>и автоматически присваиваете значение заказа своим конечным точкам в зависимости от заказа, на который они ссылаются. Совпадения с маршрута, который появляется ранее, имеют более высокий приоритет. При маршрутизации на основе соглашений учитывается порядок. В общем, маршруты с областями должны быть размещены раньше, поскольку они более специфичны, чем маршруты без области. [Выделенные обычные маршруты](#dcr) с `{*article}` уловом всех параметров маршрута, как может сделать маршрут слишком [жадным,](xref:fundamentals/routing#greedy)это означает, что он соответствует URL-адреса, которые вы намеревались быть сопоставлены с другими маршрутами. Поместите жадные маршруты позже в таблице маршрутов, чтобы предотвратить жадные матчи.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Разрешение неоднозначных действий

Когда две конечные точки совпадают с помощью реуктора, разгром должен сделать одну из следующих:

* Выберите лучшего кандидата.
* Создание исключения.

Пример:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Предыдущий контроллер определяет два действия, которые совпадают:

* Путь URL-адреса`/Products33/Edit/17`
* Данные `{ controller = Products33, action = Edit, id = 17 }`маршрута .

Это типичная схема для контроллеров MVC:

* `Edit(int)`отображает форму для отправления продукта.
* `Edit(int, Product)`обрабатывает размещенную форму.

Для решения правильного маршрута:

* `Edit(int, Product)`выбирается, когда запрос является `POST`HTTP.
* `Edit(int)`выбирается, когда [глагол HTTP](#verb) является чем-то еще. `Edit(int)`как правило, `GET`называется через .

В <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, предоставляется для разгрома, так что он может выбрать на основе метода HTTP запроса. Делает `HttpPostAttribute` `Edit(int, Product)` лучше матч, `Edit(int)`чем .

Важно понимать роль таких атрибутов, `HttpPostAttribute`как. Аналогичные атрибуты определены для других [глаголов HTTP.](#verb) В [обычной области,](#cr)обычнодля действия использовать одно и то же имя действия, когда они являются частью формы шоу, отправить рабочий процесс формы. Например, [см.](xref:tutorials/first-mvc-app/controller-methods-views#get-post)

Если в этой области не может быть <xref:System.Reflection.AmbiguousMatchException> выбранный лучший кандидат, он будет брошен, перечислив несколько совпадающих конечных точек.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Обычные названия маршрутов

Строки `"blog"` и `"default"` в следующих примерах являются обычными названиями маршрутов:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Названия маршрутов придают маршруту логическое название. Названный маршрут можно использовать для генерации URL. Использование названного маршрута упрощает создание URL-адреса, когда упорядочение маршрутов может усложнить генерацию URL. Названия маршрутов должны быть уникальными широкими приложениями.

Названия маршрутов:

* Не повлияет на сопоставление URL или обработку запросов.
* Используются только для генерации URL.

Концепция названия маршрута представлена в маршрутизации как [IEndpointNameMetadata.](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata) Имя **маршрута** терминов и **имя конечных точек:**

* Взаимозаменяемы.
* Какой из них используется в документации и коде, зависит от описанного API.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Аattributeная routing для АПЫ REST

REST AIS должны использовать функционирование атрибутов для моделирования функциональности приложения как набора ресурсов, в которых операции представлены [глаголами HTTP.](#verb)

При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов. Следующий `StartUp.Configure` код характерен для REST API и используется в следующем примере:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

В предыдущем коде, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` называется внутри, чтобы карта атрибута маршрутизаторов контроллеров.

Рассмотрим следующий пример:

* Используется `Configure` предыдущий метод.
* `HomeController`соответствует набору URL-адресов, похожих `{controller=Home}/{action=Index}/{id?}` на то, что соответствует обычному маршруту по умолчанию.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Действие `HomeController.Index` запущено для любого из `/` `/Home`путей `/Home/Index`URL, или `/Home/Index/3`.

В этом примере подчеркивается ключевая разница в программировании между направлением атрибутов и [обычной реуктором.](#cr) Маршрутизации атрибутов требует большего ввода для указания маршрута. Обычный маршрут по умолчанию обрабатывает маршруты более лаконично. Однако маршрутирование атрибутов позволяет и требует точного контроля, какие шаблоны маршрутов применяются к каждому [действию.](#action)

При разгроме атрибутов имя контроллера и имена действий не играют **никакой** роли, в которой совпадает действие. Следующий пример совпадает с теми же URL-адресами, что и предыдущий пример:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Следующий код использует замену `action` `controller`маркеров для и:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Следующий код `[Route("[controller]/[action]")]` применяется к контроллеру:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

В предыдущем коде `Index` шаблоны метода `/` должны `~/` быть готовы или к шаблонам маршрута. Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.

Просмотрите [приоритет шаблона Route](xref:fundamentals/routing#rtp) для получения информации о выборе шаблона маршрута.

## <a name="reserved-routing-names"></a>Зарезервированные имена маршрутизации

Следующие ключевые слова являются зарезервированными именами параметров маршрута при использовании контроллеров или страниц бритвы:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Использование `page` в качестве параметра маршрута с маршрутизанием атрибутов является обычной ошибкой. Это приводит к непоследовательному и запутанному поведению с генерацией URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Специальные имена параметров используются поколением URL для определения того, относится ли операция генерации URL к странице Razor или к контроллеру.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Шаблоны глаголов HTTP

ASP.NET Core имеет следующие шаблоны глагола HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [(HttpPost)](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [(Httpput)](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [(HttpDelete)](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [(Httphead)](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [(Httppatch)](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Шаблоны маршрутов

ASP.NET Core имеет следующие шаблоны маршрутов:

* Все [шаблоны глагола HTTP](#verb) являются шаблонами маршрутов.
* [(Маршрут)](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Аattributeная реуктора с атрибутами глагола Http

Рассмотрим следующий контроллер:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

В приведенном выше коде:

* Каждое действие `[HttpGet]` содержит атрибут, который ограничивает соответствие только запросам HTTP GET.
* Действие `GetProduct` включает `"{id}"` в себя `id` шаблон, поэтому `"api/[controller]"` присваивается к шаблону на контроллере. Шаблон методов `"api/[controller]/"{id}""`. Поэтому это действие только соответствует GET `/api/test2/xyz``/api/test2/123`запросы на форму,`/api/test2/{any string}`и т.д.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* Действие `GetIntProduct` содержит `"int/{id:int}")` шаблон. Часть `:int` шаблона ограничивает значения `id` маршрута строками, которые могут быть преобразованы в ряды. Запрос GET `/api/test2/int/abc`на:
  * Не совпадает с этим действием.
  * Возвращает ошибку [404 Не найденной.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Действие `GetInt2Product` содержится `{id}` в шаблоне, но `id` не ограничивает значения, которые могут быть преобразованы в ряд. Запрос GET `/api/test2/int2/abc`на:
  * Соответствует этому маршруту.
  * Привязка модели `abc` не преобразуется в несколько. Параметр `id` метода является неистежем.
  * Возвращает [400 Bad Request,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) потому`abc` что связывание модели не удалось преобразовать в ряд.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

В реукторе <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> атрибутов <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>можно <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>использовать <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>такие атрибуты, как и . Все атрибуты [глагола HTTP](#verb) принимают шаблон маршрута. В следующем примере показаны два действия, которые соответствуют тому же шаблону маршрута:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Использование пути `/products3`URL:

* Действие `MyProductsController.ListProducts` выполняется при `GET` [глаголе HTTP.](#verb)
* Действие `MyProductsController.CreateProduct` выполняется при `POST` [глаголе HTTP.](#verb)

При создании REST API редко требуется использовать `[Route(...)]` метод действия, поскольку действие принимает все методы HTTP. Лучше использовать более конкретный [атрибут глагола HTTP,](#verb) чтобы быть точным о том, что поддерживает ваш API. Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.

REST AIS должны использовать функционирование атрибутов для моделирования функциональности приложения как набора ресурсов, в которых операции представлены глаголами HTTP. Это означает, что многие операции, например, GET и POST на одном логическом ресурсе используют один и тот же URL. Маршрутизация с помощью атрибутов обеспечивает необходимый уровень контроля, позволяющий тщательно разработать схему общедоступных конечных точек API-интерфейса.

Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута. В следующем примере `id` требуется как часть пути URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Действие: `Products2ApiController.GetProduct(int)`

* Работает с URL-траекторией, как`/products2/3`
* Не работает с траекторией `/products2`URL.

Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов. Для получения дополнительной [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes)информации см.

 Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).

Для получения `[ApiController]`дополнительной информации о, см. [ApiController attribute](xref:web-api/index##apicontroller-attribute)

## <a name="route-name"></a>Имя маршрута

В следующем коде определяется имя маршрута`Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута. Названия маршрутов:

* Не повлияет на поведение сопоставления URL-адреса с разгромом.
* Используются только для генерации URL.

Имена маршрутов должны быть уникальными в пределах приложения.

Сравните предыдущий код с обычным маршрутом `id` по умолчанию, который определяет параметр как необязательный ().`{id?}` Возможность точно говоруна AIS имеет `/products` свои `/products/5` преимущества, такие как разрешение и отправка на различные действия.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Объединение маршрутов атрибутов

Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий. Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий. В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

В предшествующем примере:

* Путь `/products` URL может совпадать`ProductsApi.ListProducts`
* Путь `/products/5` URL может `ProductsApi.GetProduct(int)`совпадать с.

Оба эти действия совпадают только с HTTP, `GET` поскольку они помечены атрибутом. `[HttpGet]`

Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру. Следующий пример соответствует набору путей URL, похожих на маршрут по умолчанию.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

В следующей `[Route]` таблице объясняется атрибуты в предыдущем коде:

| Атрибут               | Комбинирует сястом`[Route("Home")]` | Определение шаблона маршрута |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Да | `"Home"` |
| `[Route("Index")]` | Да | `"Home/Index"` |
| `[Route("/")]` | **Нет** | `""` |
| `[Route("About")]` | Да | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Порядок маршрута атрибутов

Routing строит дерево и сопоставляет все конечные точки одновременно:

* Записи маршрута ведут себя так, как будто размещены в идеальном порядке.
* Наиболее конкретные маршруты имеют возможность выполнить до более общих маршрутов.

Например, маршрут `blog/search/{topic}` атрибута более специфичен, `blog/{*article}`чем маршрут атрибута, как. Маршрут `blog/search/{topic}` имеет более высокий приоритет, по умолчанию, потому что он более специфичен. Используя [обычную маршрутизм,](#cr)разработчик отвечает за размещение маршрутов в нужном порядке.

Маршруты атрибутов могут настроить <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> заказ с помощью свойства. Все [атрибуты маршрута, предусмотренные](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) рамками, включают в себя. `Order` Маршруты обрабатываются в порядке возрастания значения свойства `Order`. Порядок по умолчанию — `0`. Настройка маршрута `Order = -1` с использованием трасс перед маршрутами, которые не устанавливают заказ. Настройка маршрута `Order = 1` с использованием трасс после заказа маршрута по умолчанию.

**Избегайте** в `Order`зависимости от . Если пространство URL-пространства приложения требует четкого значения заказа для правильного маршрута, то это, скорее всего, сбивает с толку и клиентов. В целом маршрутатрибут выбирает правильный маршрут с сопоставлением URL. Если порядок по умолчанию, используемый для генерации URL, не работает, использование `Order` имени маршрута в качестве переопределения обычно проще, чем применение свойства.

Рассмотрим следующие два контроллера, `/home`которые определяют соответствие маршрута:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Запрос `/home` с предыдущим кодом бросает исключение, аналогичное следующему:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Добавление `Order` к одному из атрибутов маршрута разрешает двусмысленность:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

С предыдущим кодом `/home` `HomeController.Index` запускаетконечную точку. Чтобы добраться `MyDemoController.MyIndex`до `/home/MyIndex`запроса . **Примечание.**

* Предыдущий код является примером или плохой конструкцией routing. Он был использован `Order` для иллюстрации свойства.
* Свойство `Order` только решает двусмысленность, этот шаблон не может быть сопоставлен. Было бы лучше удалить `[Route("Home")]` шаблон.

[См. маршрут Razor Pages и конвенций приложений: Заказ маршрута](xref:razor-pages/razor-pages-conventions#route-order) для получения информации о заказе маршрута с помощью страниц Razor.

В некоторых случаях ошибка HTTP 500 возвращается с неоднозначными маршрутами. Используйте [журнал,](xref:fundamentals/logging/index) чтобы увидеть, какие конечные `AmbiguousMatchException`точки вызвали .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Замена токенов в шаблонах маршрутов «контроллер», «действие», «область»

Для удобства маршруты атрибутов поддерживают замену маркеров для зарезервированных параметров маршрута, прилагая токен в один из следующих:

* Квадратные скобки:`[]`
* Кудрявые скобки:`{}`

Токены `[action]`и `[area]` `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

В приведенном выше коде:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Матчи`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Матчи`/Products0/Edit/{id}`

Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов. Предыдущий пример ведет себя так же, как и следующий код:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Маршруты на основе атрибутов могут также сочетаться с наследованием. Это мощный в сочетании с заменой токенов. Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`генерирует уникальное название маршрута для каждого действия:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 создает уникальное имя маршрута для каждого действия.

Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Использование преобразователя параметров для настройки замены токенов

Замену токенов можно настроить, используя преобразователь параметров. Преобразователь параметров реализует <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> и преобразует значения параметров. Например, пользовательский `SlugifyParameterTransformer` трансформатор `SubscriptionManagement` параметра `subscription-management`изменяет значение маршрута на:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> является соглашением для модели приложения, которое:

* Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.
* Настраивает значения токена маршрут атрибута при замене.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Предыдущий `ListAll` метод `/subscription-management/list-all`совпадает.

`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

См [веб-документов MDN на Slug](https://developer.mozilla.org/docs/Glossary/Slug) для определения Slug.

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Несколько маршрутов атрибутов

Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию. Наиболее распространенный случай использования этой возможности — имитация поведения маршрута по умолчанию на основе соглашения, как показано в следующем примере:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Размещение нескольких атрибутов маршрута на контроллере означает, что каждый из них сочетается с каждым из атрибутов маршрута на методах действия:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Все ограничения маршрута [глагола HTTP](#verb) реализуем. `IActionConstraint`

При размещении нескольких <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> атрибутов маршрута, которые реализуются в действии:

* Каждое ограничение действия сочетается с шаблоном маршрута, применяемым к контроллеру.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Использование нескольких маршрутов на действиях может показаться полезным и мощным, лучше сохранить пространство URL вашего приложения основным и четко определены. Используйте несколько маршрутов на **действиях только** там, где это необходимо, например, для поддержки существующих клиентов.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов

Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

В предыдущем коде `[HttpPost("product/{id:int}")]` применяется ограничение маршрута. Действие `ProductsController.ShowProduct` сопоставляется только по таким `/product/3`url-адресам, как. Часть шаблона `{id:int}` маршрута ограничивает этот сегмент только нераспределенным.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Пользовательские атрибуты маршрута с помощью IRouteTemplateProvider

Все [атрибуты маршрута реализуем.](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> Время выполнения ASP.NET core:

* Ищет атрибуты на классах контроллеров и методах действий при запуске приложения.
* Использует атрибуты, `IRouteTemplateProvider` которые реализуем для построения исходного набора маршрутов.

Реализация `IRouteTemplateProvider` для определения пользовательских атрибутов маршрута. Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Предыдущий `Get` метод `Order = 2, Template = api/MyTestApi`возвращается.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Используйте модель приложения для настройки маршрутов атрибутов

Модель приложения:

* Является объектом модели, созданной при запуске.
* Содержит все метаданные, используемые ASP.NET Core для маршрутизаира и выполнения действий в приложении.

Модель приложения включает в себя все данные, собранные из атрибутов маршрута. Данные из атрибутов маршрута `IRouteTemplateProvider` обеспечиваются реализацией. Конвенций:

* Может быть написана для изменения модели приложения, чтобы настроить, как ведет себя routing.
* Прочитаны при запуске приложения.

В этом разделе показан основной пример настройки трассировки с помощью модели приложения. Следующий код делает маршруты примерно в соответствии со структурой папок проекта.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Следующий код предотвращает `namespace` применение конвенции к контроллерам, которые являются атрибутами:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Например, следующий контроллер не `NamespaceRoutingConvention`использует:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Метод `NamespaceRoutingConvention.Apply`:

* Ничего не делает, если контроллер приписывается маршрутистируем.
* Устанавливает шаблон контроллеров на `namespace`основе, `namespace` с базой удалены.

Можно `NamespaceRoutingConvention` наносить `Startup.ConfigureServices`в:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Например, рассмотрим следующий контроллер:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

В приведенном выше коде:

* База `namespace` `My.Application`.
* Полное имя предыдущего `My.Application.Admin.Controllers.UsersController`контроллера .
* Устанавливает `NamespaceRoutingConvention` шаблон контроллеров `Admin/Controllers/Users/[action]/{id?`для .

Также `NamespaceRoutingConvention` может быть применен в качестве атрибута на контроллере:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Смешанная маршрутизация с помощью атрибутов и на основе соглашений

ASP.NET приложения Core могут смешивать использование обычной реукторов и атрибутов. Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот. **Любой** атрибут маршрута на контроллере делает **все** действия в атрибуте контроллера маршрутизированными.

Атрибутная реуктора и обычная реуктора использует один и тот же двигатель.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Поколение URL и значения окружающей среды

Приложения могут использовать функции генерации URL-адресов для создания url-адресов для действий. Создание URL-адресов устраняет URL-адреса хардкодирования, делая код более надежным и обслуживаемым. В этом разделе основное внимание уделяется функциям генерации URL, предоставляемым MVC, и охватывает только основы работы генерации URL. Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).

Интерфейс <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> является основным элементом инфраструктуры между MVC и routing для генерации URL. Экземпляр `IUrlHelper` доступен через `Url` свойство в контроллерах, представлениях и компонентах представления.

В следующем примере `IUrlHelper` интерфейс используется `Controller.Url` через свойство для создания URL-адреса для другого действия.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Если приложение использует обычный маршрут по умолчанию, значение `url` переменной — строка `/UrlGeneration/Destination`пути URL. Этот путь URL создается путем маршрутизации путем объединения:

* Значения маршрута из текущего запроса, которые называются **окружающими значениями.**
* Значения, передаваемые `Url.Action` и заменяющие эти значения в шаблон маршрута:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения. Параметр маршрута, не имеюв значения, может:

* Используйте значение по умолчанию, если оно имеется.
* Пропустите, если это необязательно. Например, `id` из шаблона `{controller}/{action}/{id?}`маршрута .

Генерация URL-адреса выходит из строя, если какой-либо необходимый параметр маршрута не имеет соответствующего значения. Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.

Предыдущий пример `Url.Action` предполагает [обычную разгром.](#cr) Генерация URL работает аналогично с [помощью рекрутинга атрибутов,](#ar)хотя понятия отличаются. С обычной реуктором:

* Значения маршрута используются для расширения шаблона.
* Значения маршрута для `controller` `action` этого шаблона и обычно отображаются. Это работает, потому что URL-адреса, соответствующие реаутингу, придерживаются конвенции.

В следующем примере используется реукторатирование атрибутов:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Действие `Source` в предыдущем коде `custom/url/to/destination`генерирует .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>был добавлен в ASP.NET Core 3.0 в качестве альтернативы `IUrlHelper`. `LinkGenerator`предлагает аналогичную, но более гибкую функциональность. Каждый `IUrlHelper` метод на имеет соответствующее `LinkGenerator` семейство методов, а также.

### <a name="generating-urls-by-action-name"></a>Формирование URL-адресов по имени действия

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), и все связанные с этим перегрузки все предназначены для создания целевой точки конечных путем указания имени контроллера и имя действия.

При `Url.Action`использовании текущие значения `controller` `action` маршрута и предусмотрены временем выполнения:

* Ценность `controller` и `action` являются частью как [окружающих ценностей,](#ambient) так и ценностей. Метод `Url.Action` всегда использует текущие `action` значения `controller` и генерирует путь URL, который направляет к текущему действию.

Попытка реуктора использовать значения в окружающих значениях для заполнения информации, которая не была предоставлена при генерации URL-адреса. Рассмотрим маршрут, как `{a}/{b}/{c}/{d}` с `{ a = Alice, b = Bob, c = Carol, d = David }`окружающими значениями:

* В routing достаточно информации для создания URL-адреса без каких-либо дополнительных значений.
* Маршрутирование имеет достаточно информации, потому что все параметры маршрута имеют значение.

Если добавлено значение: `{ d = Donovan }`

* Значение `{ d = David }` игнорируется.
* Сгенерированный `Alice/Bob/Carol/Donovan`путь URL .

**Предупреждение**: Пути URL являются иерархическими. В предыдущем примере, `{ c = Cheryl }` если добавлено значение:

* Оба значения `{ c = Carol, d = David }` игнорируются.
* Больше нет значения для `d` и генерации URL сбой.
* Нужные значения `c` и `d` должны быть указаны для создания URL-  

Возможно, вы обнаружите, что `{controller}/{action}/{id?}`поразит эту проблему маршрутом по умолчанию. Эта проблема является редким `Url.Action` на практике, потому `controller` `action` что всегда четко определяет и значение.

Несколько перегрузок [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) принимают объект значений маршрута, чтобы обеспечить значения для параметров маршрута, кроме `controller` и `action`. Объект значений маршрута часто `id`используется с . Например, `Url.Action("Buy", "Products", new { id = 17 })`. Объект значения маршрута:

* По конвенции, как правило, объект анонимного типа.
* Может быть `IDictionary<>` или [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Предыдущий код `/Products/Buy/17?color=red`генерирует .

Следующий код генерирует абсолютный URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Чтобы создать абсолютный URL, используйте один из следующих:

* Перегрузка, которая `protocol`принимает . Например, предыдущий код.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), который генерирует абсолютные URIs по умолчанию.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Создание URL-адресов по маршруту

Предыдущий код продемонстрировал генерацию URL-адреса, передавая в контроллер и имя действия. `IUrlHelper`также предоставляет [url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) семейство методов. Эти методы аналогичны [Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)но они не копируют `action` текущие значения и `controller` значения маршрута. Наиболее распространенное `Url.RouteUrl`использование:

* Укажите имя маршрута для создания URL-адреса.
* Как правило, не указывается контроллер или имя действия.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Следующий файл Razor генерирует HTML `Destination_Route`ссылку на:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Создание URL-адресов в HTML и Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>предоставляет <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> методы [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) и [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) для генерации `<form>` и `<a>` элементов соответственно. Эти методы используют метод [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) для создания URL-адреса и принимают аналогичные аргументы. Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.

Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`. Обе они реализуются с помощью интерфейса `IUrlHelper`. Для получения дополнительной информации ознакомьтесь с [формами Tag Helpers в формах.](xref:mvc/views/working-with-forms)

Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Генерация URL в результатах действий

Предыдущие примеры `IUrlHelper` показали использование в контроллере. Наиболее распространенным использованием контроллера является создание URL-адреса в результате действия.

Базовые классы <xref:Microsoft.AspNetCore.Mvc.ControllerBase> и <xref:Microsoft.AspNetCore.Mvc.Controller> предоставляют удобные методы для результатов действий, ссылающихся на другое действие. Одним из типичных способов является перенаправление после приема пользовательского ввода:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Действия результаты заводских <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> методов, таких как и <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`следовать аналогичной схеме методы на .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Выделенные маршруты на основе соглашений

[Обычная маршрутизирующая система](#cr) может использовать особый вид определения маршрута, называемый [выделенным обычным маршрутом.](#dcr) В следующем примере названный `blog` маршрут представляет собой специальный обычный маршрут:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Используя предыдущие определения маршрута, `Url.Action("Index", "Home")` генерирует путь `/` URL `default` с помощью маршрута, но почему? Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.

[Выделенные обычные маршруты](#dcr) опираются на особое поведение значений по умолчанию, не имеющеющее соответствующего параметра маршрута, который не позволяет маршруту быть слишком [жадным](xref:fundamentals/routing#greedy) с генерацией URL. В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет. Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию. Генерация `blog` URL-адреса с `{ controller = Home, action = Index }` помощью `{ controller = Blog, action = Article }`сбоев, поскольку значения не совпадают с значениями. После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Области

[Области](xref:mvc/controllers/areas) — это функция MVC, используемая для организации связанных функциональных возможностей в группу в виде отдельной:

* Пространство имен для реаниматирования для действий контроллера.
* Структура Folder для представлений.

Использование областей позволяет приложению иметь несколько контроллеров с одинаковым именем, если у них есть разные области. При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`. В этом разделе рассматриваются способы взаимодействия трассировки с областями. Ознакомьтесь с [разделами Области](xref:mvc/controllers/areas) для получения подробной информации о том, как используются области с представлениями.

Следующий пример настраивает MVC для использования `area` обычного маршрута `area` `Blog`по умолчанию и маршрута для имени:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

В предыдущем <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> коде, называется `"blog_route"`для создания . Второй параметр, `"Blog"`это название области.

При сопоставлении `/Manage/Users/AddUser`пути `"blog_route"` URL, например, `{ area = Blog, controller = Users, action = AddUser }`маршрут генерирует значения маршрута. Значение `area` маршрута производится значением `area`по умолчанию для. `MapAreaControllerRoute` Маршрут, созданный, эквивалентен следующему:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

Метод `MapAreaControllerRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`). Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.

При маршрутизации на основе соглашений учитывается порядок. В общем, маршруты с областями должны быть размещены раньше, поскольку они более специфичны, чем маршруты без области.

Используя предыдущий пример, значения `{ area = Blog, controller = Users, action = AddUser }` маршрута соответствуют следующим действиям:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Атрибут [«Область»](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) — это то, что означает контроллер как часть области. Этот контроллер находится `Blog` в этом районе. Контроллеры без `[Area]` атрибута не являются членами какой-либо области и **не** совпадают, когда значение `area` маршрута обеспечивается маршрутизатием. В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Пространство имен каждого контроллера отображается здесь для полноты. Если предыдущие контроллеры используют одно и то же пространство имен, будет сгенерирована ошибка компилятора. Имена пространств классов не влияют на маршрутизацию в MVC.

Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`. Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.

<a name="aa"></a>

В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.

При выполнения действия внутри области значение `area` маршрута доступно в качестве [эмбиентного значения](#ambient) для маршрутики для использования в генерации URL. Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Следующий код генерирует `/Zebra/Users/AddUser`URL:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Определение действий

Общедоступные методы на контроллере, за исключением методов с атрибутом [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) являются действиями.

## <a name="sample-code"></a>Образец кода

 * Метод [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) включен в [загрузку образца](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) и используется для отображения информации о маршрутизании.
* [Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [(как скачать)](xref:index#how-to-download-a-sample)

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В ASP.NET Core MVC используется [ПО промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов с действиями. Маршруты определяются в коде запуска или атрибутах. Они описывают то, как пути URL-адресов должны сопоставляться с действиями. С помощью маршрутов также формируются URL-адреса (для ссылок), отправляемые в ответах.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).

В этом документе описывается взаимодействие между MVC и маршрутизацией и использование возможностей маршрутизации в типичных приложениях MVC. Подробные сведения о расширенной маршрутизации см. в статье [Маршрутизация](xref:fundamentals/routing).

## <a name="setting-up-routing-middleware"></a>Настройка ПО промежуточного слоя маршрутизации

Метод *Configure* содержит код, который выглядит примерно так:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

В вызове `UseMvc` метод `MapRoute` используется для создания одного маршрута, который называется маршрутом `default`. В большинстве приложений MVC используется маршрут с шаблоном, сходным с маршрутом `default`.

Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` может сопоставлять путь URL-адреса, например `/Products/Details/5`, и извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем разбивки пути на лексемы. MVC попытается найти контроллер с именем `ProductsController` и выполнить действие `Details`:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Обратите внимание на то, что в этом примере привязка модели будет использовать значение `id = 5`, чтобы присвоить параметру `id` значение `5` при вызове действия. Дополнительные сведения см. в разделе [Привязка модели](../models/model-binding.md).

Использование маршрута `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Шаблон маршрута:

* `{controller=Home}` определяет `Home` в качестве объекта `controller` по умолчанию.

* `{action=Index}` определяет `Index` в качестве объекта `action` по умолчанию.

* `{id?}` определяет необязательный параметр `id`.

Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления. Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

`"{controller=Home}/{action=Index}/{id?}"` может сопоставляться с путем URL-адреса `/` и выдает значения маршрута `{ controller = Home, action = Index }`. Для объектов `controller` и `action` используются значения по умолчанию. `id` не имеет значения, так как в пути URL-адреса нет соответствующего сегмента. MVC будет использовать эти значения маршрута для выбора действия `HomeController` и `Index`:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

При использовании этого определения контроллера и шаблона маршрута действие `HomeController.Index` будет выполняться для любых из следующих путей URL-адресов:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Универсальный метод `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Можно использовать вместо следующего метода:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` и `UseMvcWithDefaultRoute` добавляют экземпляр `RouterMiddleware` в конвейер ПО промежуточного слоя. MVC не взаимодействует с ПО промежуточного слоя напрямую, а использует маршрутизацию для обработки запросов. MVC подключается к маршрутам посредством экземпляра `MvcRouteHandler`. Код метода `UseMvc` имеет примерно следующий вид:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

Метод `UseMvc` не определяет маршруты напрямую, а добавляет заполнитель в коллекцию маршрутов для маршрута `attribute`. Перегрузка `UseMvc(Action<IRouteBuilder>)` позволяет добавлять собственные маршруты, а также поддерживает маршрутизацию с помощью атрибутов.  Метод `UseMvc` и все его варианты добавляют заполнитель для маршрута на основе атрибутов. Маршрутизация с помощью атрибутов доступна всегда вне зависимости от того, как настроен метод `UseMvc`. Метод `UseMvcWithDefaultRoute` определяет маршрут по умолчанию и поддерживает маршрутизацию с помощью атрибутов. В разделе [Маршрутизация с помощью атрибутов](#attribute-routing-ref-label) приводятся более подробные сведения о маршрутизации с помощью атрибутов.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Маршрутизация на основе соглашений

Маршрут `default`:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Предыдущий код является примером обычной реуктора. Этот стиль называется обычной маршрутизаний, поскольку он устанавливает *конвенцию* для путей URL:

* Первый сегмент пути отображает имя контроллера.
* Вторая карта к названию действия.
* Третий сегмент используется для `id`дополнительного . `id`карты для модели сущности.

При использовании этого маршрута `default` путь URL-адреса `/Products/List` сопоставляется с действием `ProductsController.List`, а путь `/Blog/Article/17` сопоставляется с `BlogController.Article`. Такое сопоставление основывается **только** на именах контроллера и действия, но не на пространствах имен, расположениях исходных файлов или параметрах метода.

> [!TIP]
> Использование маршрутизации на основе соглашений с маршрутом по умолчанию позволяет быстро создавать приложение, не придумывая новый шаблон URL-адреса для каждого определяемого действия. В случае с приложением с действиями в стиле CRUD единообразие URL-адресов для всех контроллеров позволяет упростить код и сделать пользовательский интерфейс более предсказуемым.

> [!WARNING]
> Параметр `id` определяется в шаблоне маршрута как необязательный. Это означает, что действия могут выполняться, даже если идентификатор не указан в URL-адресе. Как правило, если параметр `id` отсутствует в URL-адресе, привязка модели присваивает ему значение `0`, и в результате в базе данных не будет найдена сущность, соответствующая `id == 0`. Маршрутизация с помощью атрибутов обеспечивает детальный контроль, позволяя настраивать идентификатор как обязательный лишь для некоторых действий. В документации необязательные параметры, такие как `id`, будут включаться, только если они, скорее всего, могут использоваться в соответствующей ситуации.

## <a name="multiple-routes"></a>Несколько маршрутов

В метод `UseMvc` можно добавить несколько маршрутов, добавив дополнительные вызовы `MapRoute`. Таким образом можно определить несколько соглашений или добавить маршруты на основе соглашений, предназначенные для определенного действия, например:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Маршрут `blog` здесь — это *выделенный маршрут на основе соглашения*. Это означает, что он использует систему маршрутизации на основе соглашений, но предназначен для определенного действия. Так как параметры `controller` и `action` отсутствуют в шаблоне маршрута, они могут иметь только значения по умолчанию, поэтому этот маршрут всегда будет сопоставляться с действием `BlogController.Article`.

Маршруты в коллекции маршрутов упорядочены и обрабатываются в порядке добавления. Поэтому в этом примере маршрут `blog` будет проверяться перед маршрутом `default`.

> [!NOTE]
> *Выделенные обычные маршруты* часто используют `{*article}` параметры **маршрута catch-all,** например, для захвата оставшейся части пути URL. Из-за этого маршрут может оказаться слишком универсальным, то есть он будет соответствовать URL-адресам, которые должны сопоставляться с другими маршрутами. Чтобы избежать этой проблемы, такие универсальные маршруты следует помещать в конце таблицы маршрутов.

### <a name="fallback"></a>Откат

В ходе обработки запроса MVC проверяет, можно ли найти контроллер и действие в приложении с помощью предоставленных значений маршрута. Если нет действия, соответствующего значениям, маршрут считается не сопоставленным, и проверяется следующий маршрут. Этот процесс называется *откатом* и призван упростить обработку случаев, когда маршруты на основе соглашений перекрываются.

### <a name="disambiguating-actions"></a>Разрешение неоднозначности действий

Если при маршрутизации найдены два соответствующих действия, платформа MVC должна устранить неоднозначность, выбрав наиболее подходящее из них, или создать исключение. Пример:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Этот контроллер определяет два действия, которые соответствуют пути URL-адреса `/Products/Edit/17` и маршрутизируют данные `{ controller = Products, action = Edit, id = 17 }`. Это типичный шаблон для контроллеров MVC, в котором метод `Edit(int)` отображает форму для изменения сведений о продукте, а метод `Edit(int, Product)` обрабатывает отправленную форму. Чтобы это было возможным, платформа MVC должна выбрать `Edit(int, Product)` для HTTP-запроса `POST` и `Edit(int)` для любой другой HTTP-команды.

`HttpPostAttribute` (`[HttpPost]`) — это реализация интерфейса `IActionConstraint`, которая позволяет выбрать действие, только если HTTP-команда — `POST`. Наличие интерфейса `IActionConstraint` делает метод `Edit(int, Product)` более подходящим вариантом, чем `Edit(int)`, поэтому `Edit(int, Product)` будет проверяться первым.

Пользовательские реализации `IActionConstraint` потребуется создавать только в особых ситуациях, однако важно понимать роль таких атрибутов, как `HttpPostAttribute`, — аналогичные атрибуты определены для других HTTP-команд. При маршрутизации на основе соглашений для действий часто используются одинаковые имена в рамках рабочего процесса `show form -> submit form`. Удобство такого шаблона станет очевидным после ознакомления с разделом [Сведения об интерфейсе IActionConstraint](#understanding-iactionconstraint).

Если найдено несколько совпадений и MVC не может определить наиболее подходящий маршрут, создается исключение `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Имена маршрутов

Строки `"blog"` и `"default"` в следующих примерах представляют собой имена маршрутов:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Имя маршрута — это логическое имя, которое позволяет использовать именованный маршрут для формирования URL-адреса. Имена значительно упрощают создание URL-адресов, если оно представляет сложность из-за порядка маршрутов. Имена маршрутов должны быть уникальными в пределах приложения.

Имена маршрутов не влияют на сопоставление URL-адресов или обработку запросов; они служат только для формирования URL-адресов. В статье [Маршрутизация](xref:fundamentals/routing) приводятся более подробные сведения о формировании URL-адресов, в том числе во вспомогательных объектах MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Маршрутизация с помощью атрибутов

При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов. В приведенном ниже примере в методе `Configure` используется `app.UseMvc();`, и маршрут не передается. `HomeController` будет соответствовать набору URL-адресов, аналогичных тем, которым соответствует маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Действие `HomeController.Index()` будет выполняться для любого из путей URL-адресов `/`, `/Home` или `/Home/Index`.

> [!NOTE]
> В этом примере показано ключевое различие между маршрутизацией с помощью атрибутов и маршрутизацией на основе соглашений. При использовании маршрутизации с помощью атрибутов для указания маршрута требуется больше входных данных; маршрут по умолчанию на основе соглашения более лаконичен. Однако маршрутизация с помощью атрибутов обеспечивает более точный контроль над тем, какие шаблоны маршрутов применяются к каждому действию, и требует такого контроля.

В случае с маршрутизацией с помощью атрибутов имя контроллера и имена действий **не** имеют значения при выборе действия. В этом примере сопоставление будет производиться с теми же URL-адресами, что и в предыдущем.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Приведенные выше шаблоны маршрутов не определяют параметры маршрутов для `action`, `area` и `controller`. По сути, эти параметры маршрутов не разрешены в маршрутах на основе атрибутов. Так как шаблон маршрута уже связан с действием, не имеет смысла анализировать имя действия в URL-адресе.

## <a name="attribute-routing-with-httpverb-attributes"></a>Маршрутизация с помощью атрибутов Http[Verb]

При маршрутизации с помощью атрибутов также могут использоваться атрибуты `Http[Verb]`, такие как `HttpPostAttribute`. Все эти атрибуты могут принимать шаблон маршрута. В этом примере показаны два действия, которые совпадают с одним шаблоном маршрута:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Для такого пути URL-адреса, как `/products`, действие `ProductsApi.ListProducts` будет выполнено, если HTTP-командой является `GET`, а действие `ProductsApi.CreateProduct` будет выполнено, если HTTP-командой является `POST`. При маршрутизации с помощью атрибутов URL-адрес сначала сопоставляется с набором шаблоном маршрутов, определяемых атрибутами маршрутов. После того как найден соответствующий шаблон маршрута, применяются ограничения `IActionConstraint` для определения действий, которые могут быть выполнены.

> [!TIP]
> При разработке REST API редко приходится использовать `[Route(...)]` для метода действия, так как действие принимает все методы HTTP. Предпочтительнее применять более конкретные атрибуты `Http*Verb*Attributes`, чтобы точно определить поддерживаемые API возможности. Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.

Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута. В этом примере параметр `id` является обязательным в пути URL-адреса.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Действие `ProductsApi.GetProduct(int)` будет выполнено для такого пути URL-адреса, как `/products/3`, но не для такого пути URL-адреса, как `/products`. Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).

## <a name="route-name"></a>Имя маршрута

В следующем коде определяется *имя маршрута*`Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута. Они не влияют на то, как производится сопоставление URL-адресов при маршрутизации, и служат только для формирования URL-адресов. Имена маршрутов должны быть уникальными в пределах приложения.

> [!NOTE]
> Сравните это поведение с *маршрутом по умолчанию* на основе соглашения, в котором параметр `id` определяется как необязательный (`{id?}`). Такая возможность точно указывать интерфейсы API имеет преимущества. Например, она позволяет направлять `/products` и `/products/5` в разные действия.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Объединение маршрутов

Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий. Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий. В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

В этом примере путь URL-адреса `/products` может соответствовать `ProductsApi.ListProducts`, а путь URL-адреса `/products/5` — `ProductsApi.GetProduct(int)`. Оба эти действия соответствуют только HTTP-запросу `GET`, так как они помечены атрибутом `HttpGetAttribute`.

Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру. Этот пример соответствует такому же набору путей URL-адресов, что и *маршрут по умолчанию*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Упорядочение маршрутов на основе атрибутов

В отличие от обычных маршрутов, которые выполняются в определенном порядке, маршрутирование атрибутов строит дерево и совпадает со всеми маршрутами одновременно. Это равносильно тому, как если бы записи маршрутов находились в идеальном порядке: наиболее конкретные маршруты имеют возможность выполнения перед более общими.

Например, такой маршрут, как `blog/search/{topic}`, является более конкретным по сравнению с `blog/{*article}`. С логической точки зрения, маршрут `blog/search/{topic}` по умолчанию выполняется первым, так как это единственная разумная очередность. При использовании маршрутизации на основе соглашений разработчик отвечает за расположение маршрутов в нужном порядке.

При маршрутизации с помощью атрибутов порядок может настраиваться с помощью свойства `Order` всех атрибутов маршрутов, предоставляемых платформой. Маршруты обрабатываются в порядке возрастания значения свойства `Order`. Порядок по умолчанию — `0`. Маршрут, для которого задано значение `Order = -1`, будет выполняться перед маршрутами, для которых порядок не задан. Маршрут, для которого задано значение `Order = 1`, будет выполняться после маршрутов с порядком по умолчанию.

> [!TIP]
> Старайтесь не использовать свойство `Order`. Если для правильной маршрутизации в пространстве URL-адресов требуются явно заданные значения порядка, скорее всего, это будет вызывать путаницу и в среде клиентов. Как правило, при маршрутизации с помощью атрибутов правильный маршрут выбирается посредством сопоставления URL-адресов. Если порядок по умолчанию для формирования URL-адресов не работает, использовать имя маршрута в качестве переопределения, как правило, проще, чем применять свойство `Order`.

Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию. Сведения о порядке маршрутизации в Razor Pages см. в статье [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) (Маршрутизация и соглашения в приложении Razor Pages: порядок маршрутизации).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Замена токенов в шаблонах маршрутов ([controller], [action], [area])

Для удобства атрибут маршруты поддерживают *замену маркера,* прикрепив`[` `]`токен в квадратные скобки (, ). Токены `[action]`, `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут. В следующем примере действия могут соответствовать путям URL-адресов, как описано в комментариях:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов. Приведенный выше пример будет работать так же, как следующий код:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Маршруты на основе атрибутов могут также сочетаться с наследованием. Эта возможность особенно эффективна при использовании вместе с заменой токенов.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов. `[Route("[controller]/[action]", Name="[controller]_[action]")]` создает уникальное имя маршрута для каждого действия.

Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Использование преобразователя параметров для настройки замены токенов

Замену токенов можно настроить, используя преобразователь параметров. Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров. Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.

`RouteTokenTransformerConvention` является соглашением для модели приложения, которое:

* Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.
* Настраивает значения токена маршрут атрибута при замене.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Несколько маршрутов

Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию. Наиболее распространенный случай использования этой возможности — имитация поведения *маршрута по умолчанию на основе соглашения*, как показано в следующем примере:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Добавление нескольких атрибутов маршрута для контроллера означает, что каждый из них будет объединяться с каждым из атрибутов маршрута, определенных для методов действий.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Если несколько атрибутов маршрута (реализующих интерфейс `IActionConstraint`) добавлены для действия, каждое ограничение действия объединяется с шаблоном маршрута из атрибута, в котором оно определено.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Хотя использование нескольких маршрутов к действиям может показаться очень эффективной возможностью, лучше, чтобы пространство URL-адресов приложения оставалось простым и четко организованным. Используйте несколько маршрутов к действиям только там, где это необходимо, например для поддержки существующих клиентов.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов

Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Пользовательские атрибуты маршрута с использованием `IRouteTemplateProvider`

Все атрибуты маршрутов, предоставляемые платформой ( `[Route(...)]`, `[HttpGet(...)]` и т. д.), реализуют интерфейс `IRouteTemplateProvider`. MVC ищет атрибуты в классах контроллеров и методах действий при запуске приложения и использует те из них, которые реализуют интерфейс `IRouteTemplateProvider`, для формирования начального набора маршрутов.

Вы можете реализовать интерфейс `IRouteTemplateProvider` для определения собственных атрибутов маршрутов. Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Атрибут в приведенном выше примере автоматически присваивает шаблону `Template` значение `"api/[controller]"` при применении `[MyApiController]`.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Настройка маршрутов на основе атрибутов с помощью модели приложения

*Модель приложения* — это объектная модель, которая создается при запуске со всеми метаданными, используемыми платформой MVC для маршрутизации и выполнения действий. *Модель приложения* включает в себя все данные, собранные из атрибутов маршрутов (посредством интерфейса `IRouteTemplateProvider`). Вы можете создать *соглашения*, чтобы изменить модель приложения при запуске с целью настройки маршрутизации. В этом разделе приводится простой пример настройки маршрутизации с помощью модели приложения.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Смешанная маршрутизация с помощью атрибутов и на основе соглашений

В приложениях MVC маршрутизация с помощью атрибутов и маршрутизация на основе соглашений могут использоваться вместе. Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.

Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов. При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов. Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот. **Любой** атрибут маршрута контроллера делает все действия в атрибуте контроллера маршрутизируемыми.

> [!NOTE]
> Два типа систем маршрутизации отличает процесс, выполняемый после нахождения URL-адреса, соответствующего шаблону маршрута. При маршрутизации на основе соглашений значения маршрута из соответствия используются для выбора действия и контроллера из таблицы подстановки, содержащей все действия с маршрутизацией на основе соглашений. При маршрутизации с помощью атрибутов каждый шаблон уже связан с действием, и дальнейшая подстановка не требуется.

## <a name="complex-segments"></a>Сложные сегменты

Сложные сегменты (например, `[Route("/dog{token}cat")]`) обрабатываются путем "нежадного" сопоставления литералов справа налево. Описание см. в [исходном коде](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Дополнительные сведения см. в [этой проблеме](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Формирование URL-адреса

Приложения MVC могут использовать функции формирования URL-адреса, предоставляемые системой маршрутизации, для создания URL-ссылок на действия. Формирование URL-адресов устраняет необходимость в их жестком задании, что делает код надежнее и проще в обслуживании. В этом разделе рассматриваются функции формирования URL-адреса, предоставляемые платформой MVC, и описываются лишь базовые принципы их работы. Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).

Интерфейс `IUrlHelper` — это базовый компонент инфраструктуры, обеспечивающий взаимодействие между MVC и системой маршрутизации для формирования URL-адресов. Доступ к экземпляру `IUrlHelper` в контроллерах, представлениях и компонентах представлений можно получить посредством свойства `Url`.

В этом примере интерфейс `IUrlHelper` используется посредством свойства `Controller.Url` для формирования URL-адреса другого действия.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Если в приложении применяется маршрут по умолчанию на основе соглашения, значением переменной `url` будет строка с путем URL-адреса `/UrlGeneration/Destination`. Этот путь URL-адреса создается системой маршрутизации путем объединения значений маршрута из текущего запроса (значения окружения) со значениями, переданными в `Url.Action`, и подстановки этих значений в шаблон маршрута.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения. Параметр маршрута, у которого нет значения, может принимать значение по умолчанию, если оно имеется, или пропускаться, если он является необязательным (как в случае с параметром `id` в этом примере). Сформировать URL-адрес не удастся, если у любого из обязательных параметров маршрута не будет соответствующего значения. Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.

В примере `Url.Action` выше предполагается использование маршрутизации на основе соглашений, однако формирование URL-адреса происходит аналогичным образом и в случае с маршрутизацией с помощью атрибутов, хотя принципы иные. При применении маршрутизации на основе соглашений шаблон расширяется с помощью значений маршрута, и значения маршрута для `controller` и `action` обычно находятся в этом шаблоне. Это возможно по той причине, что URL-адреса, сопоставленные системой маршрутизации, следуют *соглашению*. При маршрутизации с помощью атрибутов значения маршрута для `controller` и `action` не могут находиться в шаблоне. Вместо этого они применяются для поиска нужного шаблона.

В этом примере используется маршрутизация с помощью атрибутов:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC создает таблицу подстановки, содержащую все действия с маршрутизацией с помощью атрибутов, и сопоставляет значения `controller` и `action` для выбора шаблона маршрута, с помощью которого должен формироваться URL-адрес. В приведенном выше примере создается URL-адрес `custom/url/to/destination`.

### <a name="generating-urls-by-action-name"></a>Формирование URL-адресов по имени действия

`Url.Action` (`IUrlHelper` . `Action`) и все связанные перегрузки предполагают необходимость указания целевого объекта путем задания имени контроллера и имени действия.

> [!NOTE]
> При использовании метода `Url.Action` текущие значения маршрута для `controller` и `action` уже определены: значения `controller` и `action` включены как в *значения окружения, так и в * **обычные** *значения*. Метод `Url.Action` всегда использует текущие значения `action` и `controller` и формирует путь URL-адреса, ведущий к текущему действию.

При формировании URL-адреса система маршрутизации пытается использовать значения окружения для заполнения сведений, которые вы не предоставили. При использовании такого маршрута, как `{a}/{b}/{c}/{d}`, и значений окружения `{ a = Alice, b = Bob, c = Carol, d = David }` система маршрутизации имеет достаточно информации для формирования URL-адреса без дополнительных значений, так как все параметры маршрута имеют значения. Если добавить значение `{ d = Donovan }`, значение `{ d = David }` не будет учитываться, и будет сформирован путь URL-адреса `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Пути URL-адресов являются иерархическими. Если в приведенном выше примере добавить значение `{ c = Cheryl }`, пропущены будут оба значения `{ c = Carol, d = David }`. В этом случае параметр `d` больше не имеет значения и сформировать URL-адрес не удастся. Потребуется указать нужные значения для параметров `c` и `d`.  Эта проблема может возникнуть с маршрутом по умолчанию (`{controller}/{action}/{id?}`), но на практике она встречается редко, так как `Url.Action` всегда явным образом задает значения `controller` и `action`.

Более длинные перегрузки `Url.Action` также принимают дополнительный объект *значений маршрута* для предоставления значений параметров маршрута, отличных от `controller` и `action`. Чаще всего он применяется с параметром `id`, например `Url.Action("Buy", "Products", new { id = 17 })`. В соответствии с соглашением объект *значений маршрута* обычно имеет анонимный тип, но это может быть также экземпляр `IDictionary<>` или *обычный объект .NET*. Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Чтобы создать абсолютный URL-адрес, используйте перегрузку, принимающую `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Формирование URL-адресов по маршруту

В приведенном выше коде демонстрировалось формирование URL-адреса путем передачи имен контроллера и действия. Интерфейс `IUrlHelper` также предоставляет семейство методов `Url.RouteUrl`. Эти методы похожи на `Url.Action`, но они не копируют текущие значения `action` и `controller` в значения маршрута. Наиболее распространенный способ их применения — указание имени определенного маршрута, который должен использоваться для формирования URL-адреса, как правило, *без* указания имени контроллера или действия.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Формирование URL-адресов в HTML

Интерфейс `IHtmlHelper` предоставляет методы `HtmlHelper``Html.BeginForm` и `Html.ActionLink` для создания элементов `<form>` и `<a>` соответственно. Эти методы используют метод `Url.Action` для формирования URL-адреса и принимают одинаковые аргументы. Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.

Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`. Обе они реализуются с помощью интерфейса `IUrlHelper`. Дополнительные сведения см. в статье [Работа с формами](../views/working-with-forms.md).

Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Формирование URL-адресов в результатах действий

В предыдущих примерах было продемонстрировано использование интерфейса `IUrlHelper` в контроллере, хотя чаще всего URL-адреса формируются в контроллерах в рамках результата действия.

Базовые классы `ControllerBase` и `Controller` предоставляют удобные методы для результатов действий, ссылающихся на другое действие. Одним из типичных способов их применения является перенаправление после принятия входных данных пользователя.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Фабричные методы результатов действий по своей структуре похожи на методы интерфейса `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Выделенные маршруты на основе соглашений

При маршрутизации на основе соглашений может использоваться особый тип определения маршрута — *выделенный маршрут на основе соглашения*. В приведенном ниже примере маршрут с именем `blog` является выделенным маршрутом на основе соглашения.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

При использовании таких определений маршрутов метод `Url.Action("Index", "Home")` создаст путь URL-адреса `/` с маршрутом `default`. В чем же причина? Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.

В случае с выделенными маршрутами на основе соглашений используется особое поведение значений по умолчанию, для которых нет соответствующих параметров маршрута. Благодаря ему маршрут не может быть слишком универсальным при формировании URL-адреса. В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет. Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию. Сформировать URL-адрес с помощью `blog` не удастся, так как значения `{ controller = Home, action = Index }` не соответствуют `{ controller = Blog, action = Article }`. После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Области

[Области](areas.md) — это возможность MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен маршрутизации (для действий контроллеров) и структуры папок (для представлений). Благодаря областям приложение может иметь несколько контроллеров с одинаковыми именами, если у них будут разные *области*. При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`. В этом разделе рассматривается взаимодействие системы маршрутизации с областями. Подробные сведения об использовании областей с представлениями см. в статье [Области](areas.md).

В следующем примере в MVC настраивается использование маршрута на основе соглашения по умолчанию и *маршрута области* для области с именем `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Результатом сопоставления первого маршрута с таким путем URL-адреса, как `/Manage/Users/AddUser`, будут значения маршрута `{ area = Blog, controller = Users, action = AddUser }`. Значение маршрута `area` получается на основе значения по умолчанию для параметра `area`. По сути, маршрут, создаваемый методом `MapAreaRoute`, эквивалентен следующему:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

Метод `MapAreaRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`). Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.

> [!TIP]
> При маршрутизации на основе соглашений учитывается порядок. Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.

В предыдущем примере значения маршрута будут соответствовать следующему действию:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` обозначает контроллер в рамках области. В таком случае говорят, что контроллер находится в области `Blog`. Контроллеры без атрибута `[Area]` не входят ни в одну область и **не** будут соответствовать маршруту, если система маршрутизации предоставляет значение маршрута `area`. В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Пространство имен каждого контроллера приведено здесь для полноты. В противном случае между контроллерами возник бы конфликт именования, и произошла бы ошибка компилятора. Имена пространств классов не влияют на маршрутизацию в MVC.

Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`. Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.

> [!NOTE]
> В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.

При выполнении действия внутри области значение маршрута для `area` будет доступно как *значение окружения*, которое система маршрутизации использует для формирования URL-адреса. Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Основные сведения об интерфейсе IActionConstraint

> [!NOTE]
> В этом разделе описываются внутренние принципы работы платформы и то, как MVC выбирает действие, которое необходимо выполнить. Типичному приложению пользовательская реализация `IActionConstraint` не требуется.

Скорее всего, вы уже пользовались интерфейсом `IActionConstraint`, даже если не знакомы с ним. Атрибут `[HttpGet]` и сходные атрибуты `[Http-VERB]` реализуют интерфейс `IActionConstraint`, чтобы ограничить выполнение метода действия.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

В случае с маршрутом по умолчанию на основе соглашения путь URL-адреса `/Products/Edit` даст значения `{ controller = Products, action = Edit }`, которые будут соответствовать **обоим** приведенным здесь действиям. В терминологии `IActionConstraint` можно сказать, что оба действия считаются кандидатами, так как они оба соответствуют данным маршрута.

Когда метод `HttpGetAttribute` выполняется, он сообщает, что *Edit()* является соответствием для запроса *GET*, но не является соответствием для каких-либо иных HTTP-команд. Для действия `Edit(...)` ограничения не определены, поэтому оно соответствует любой HTTP-команде. Поэтому если рассматривать запрос `POST`, соответствием будет только `Edit(...)`. Однако в случае с запросом `GET` соответствовать могут оба действия, хотя действие с `IActionConstraint` всегда считается *имеющим приоритет*. Таким образом, поскольку действие `Edit()` имеет атрибут `[HttpGet]`, оно считается более конкретным и будет выбрано в случае соответствия обоих действий.

По сути, интерфейс `IActionConstraint` представляет собой своего рода *перегрузку*, но вместо перегрузки методов с одинаковыми именами он перегружает действия, соответствующие одному и тому же URL-адресу. При маршрутизации с помощью атрибутов также применяется интерфейс `IActionConstraint`, в результате чего кандидатами могут считаться действия из разных контроллеров.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Реализация IActionConstraint

Самый простой способ реализовать интерфейс `IActionConstraint` — создать класс, производный от `System.Attribute`, и добавить его в действия и контроллеры. MVC автоматически обнаруживает экземпляры `IActionConstraint`, применяемые как атрибуты. Вы можете использовать модель приложения для применения ограничений, и это, пожалуй, самый гибкий подход, так как он позволяет метапрограммировать способ их применения.

В следующем примере ограничение выбирает действие, основанное на *коде страны,* из данных маршрута. [Полный пример можно найти в GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Вы отвечаете за реализацию метода `Accept` и определение очередности применения ограничений. В этом случае метод `Accept` возвращает значение `true`, означающее, что действие является соответствующим при соответствии значения маршрута `country`. Таким образом, он позволяет возвращаться к действию без атрибута, чем отличается от метода `RouteValueAttribute`. В примере показано, что если определено действие `en-US`, то для кода страны `fr-FR` будет выбран более общий контроллер, к которому не применяется ограничение `[CountrySpecific(...)]`.

Свойство `Order` определяет, к какому *этапу* относится ограничение. Ограничения действий применяются группами в соответствии со значением `Order`. Например, все предоставляемые платформой атрибуты HTTP-методов имеют одинаковое значение `Order`, благодаря чему они выполняются на одном этапе. Чтобы реализовать требуемые политики, можно использовать любое количество этапов.

> [!TIP]
> Чтобы выбрать значение `Order`, подумайте, должно ли ограничение применяться перед выполнением HTTP-методов. Чем меньше значение, тем раньше применяется ограничение.

::: moniker-end
