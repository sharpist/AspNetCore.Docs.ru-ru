---
title: Пользовательские модули форматирования для веб-API в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать пользовательские модули форматирования для веб-интерфейсов API в ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408868"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Пользовательские модули форматирования для веб-API в ASP.NET Core

[Kirk Ларкин](https://twitter.com/serpent5) и [Tom Dykstra)](https://github.com/tdykstra).

ASP.NET Core MVC поддерживает обмен данными в веб-API с помощью форматировщиков ввода и вывода. Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding). Модули форматирования выходных данных используются для [форматирования ответов](xref:web-api/advanced/formatting).

Платформа предоставляет встроенные форматировщики ввода и вывода для JSON и XML. Доступен только встроенный форматировщик вывода для обычного текста, но не форматировщик ввода для обычного текста.

В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования. Пример пользовательского модуля форматирования ввода обычного текста см. в разделе [текстплаининпутформаттер](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) на GitHub.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Когда следует использовать пользовательские модули форматирования

Используйте пользовательский модуль форматирования, чтобы добавить поддержку для типа содержимого, который не обрабатывается модулями форматирования булт.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Общие сведения об использовании пользовательского модуля форматирования

Чтобы создать пользовательский модуль форматирования, сделайте следующее:

* Для сериализации данных, отправляемых клиенту, создайте класс модуля форматирования выходных данных.
* Для данных десериалзинг, полученных от клиента, создайте класс модуля форматирования ввода.
* Добавьте экземпляры классов модулей форматирования в `InputFormatters` коллекции и `OutputFormatters` в [мвкоптионс](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

## <a name="how-to-create-a-custom-formatter-class"></a>Создание пользовательского класса модуля форматирования

Чтобы создать модуль форматирования, выполните указанные ниже действия.

* Наследуйте класс от подходящего базового класса. Пример приложения является производным от <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> и <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Укажите в конструкторе допустимые типы передаваемых данных и кодировки.
* Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> и <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>.
* Переопределите методы <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> и `WriteResponseBodyAsync`.

В следующем коде показан `VcardOutputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Наследование от подходящего базового класса

Для текстовых типов данных (например, vCard) произведите наследование от базового класса [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) или [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Для двоичных типов произведите наследование от базового класса [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) или [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Указание допустимых типов передаваемых данных и кодировок.

Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Класс модуля форматирования **не** может использовать внедрение конструктора для своих зависимостей. Например, `ILogger<VcardOutputFormatter>` нельзя добавить в конструктор в качестве параметра. Для доступа к службам используйте объект контекста, который передается методам. Пример кода в этой статье и [Пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) показывает, как это сделать.

### <a name="override-canreadtype-and-canwritetype"></a>Переопределение Канреадтипе и Канвритетипе

Укажите тип для десериализации или сериализации из путем переопределения `CanReadType` `CanWriteType` методов или. Например, создание текста vCard из `Contact` типа и наоборот.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>Метод CanWriteResult

В некоторых сценариях `CanWriteResult` должен быть переопределен, а не `CanWriteType` . Используйте `CanWriteResult`, если выполняются указанные ниже условия.

* Метод действия возвращает класс модели.
* Существуют производные классы, которые могут возвращаться во время выполнения.
* Производный класс, возвращенный действием, должен быть известен во время выполнения.

Например, предположим, что метод действия:

* Сигнатура возвращает `Person` тип.
* Может возвращать `Student` `Instructor` тип или, производный от `Person` . 

Чтобы модуль форматирования обрабатывал только `Student` объекты, проверьте тип [объекта](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) в объекте контекста, предоставленном `CanWriteResult` методу. Когда метод действия возвращает `IActionResult` :

* Нет необходимости использовать `CanWriteResult` .
* `CanWriteType`Метод получает тип среды выполнения.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Переопределение Реадрекуестбодясинк и Вритереспонсебодясинк

Десериализация или сериализация выполняется в `ReadRequestBodyAsync` или `WriteResponseBodyAsync` . В следующем примере показано, как получить службы из контейнера внедрения зависимостей. Не удается получить службы из параметров конструктора.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Настройка использования пользовательского модуля форматирования в MVC

Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Модули форматирования обрабатываются в порядке добавления. Первый модуль имеет приоритет.

## <a name="the-completed-vcardinputformatter-class"></a>Завершенный `VcardInputFormatter` класс

В следующем коде показан `VcardInputFormatter` класс из [примера](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Тестирование приложения

[Запустите пример приложения для этой статьи, в](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)котором реализованы базовые модули форматирования входных и выходных данных vCard. Приложение считывает и записывает визитные карточки следующего вида:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Чтобы просмотреть выходные данные vCard, запустите приложение и отправьте запрос GET с заголовком Accept `text/vcard` в `https://localhost:5001/api/contacts` .

Чтобы добавить vCard в коллекцию контактов в памяти, выполните следующие действия.

* Отправка `Post` запроса `/api/contacts` с помощью такого средства, как POST.
* В качестве заголовка `Content-Type` установите `text/vcard`.
* Задайте `vCard` текст в тексте, отформатированный как в предыдущем примере.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
