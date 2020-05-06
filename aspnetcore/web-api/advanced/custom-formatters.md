---
title: Пользовательские модули форматирования для веб-API в ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать и использовать пользовательские модули форматирования для веб-интерфейсов API в ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777323"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Пользовательские модули форматирования для веб-API в ASP.NET Core

Автор: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra)

ASP.NET Core MVC поддерживает обмен данными в веб-API с помощью форматировщиков ввода и вывода. Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding). Модули форматирования выходных данных используются для [форматирования ответов](xref:web-api/advanced/formatting).

Платформа предоставляет встроенные форматировщики ввода и вывода для JSON и XML. Доступен только встроенный форматировщик вывода для обычного текста, но не форматировщик ввода для обычного текста.

В этой статье показано, как добавить поддержку дополнительных форматов, создав пользовательские модули форматирования. Пример пользовательского форматировщика ввода для обычного текста см. в описании [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) на GitHub.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Когда следует использовать пользовательские модули форматирования

Используйте пользовательский форматировщик, если необходимо, чтобы процесс [согласования содержимого](xref:web-api/advanced/formatting#content-negotiation) поддерживал тип содержимого, который не поддерживается встроенными форматировщиками.

Например, если некоторые из клиентов вашего веб-интерфейса API могут работать с форматом [Protobuf](https://github.com/google/protobuf), для обмена данными с ними может быть желательно использовать этот формат, так как он более эффективен. Вам может также потребоваться реализовать отправку имен и адресов веб-интерфейсом API в формате [vCard](https://wikipedia.org/wiki/VCard), распространенном формате для передачи контактных данных. В образце приложения, представленном в этой статье, реализуется простой модуль форматирования vCard.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Общие сведения об использовании пользовательского модуля форматирования

Чтобы создать и использовать пользовательский модуль форматирования, выполните указанные ниже действия.

* Создайте класс модуля форматирования выходных данных, если необходимо сериализовать данные, отправляемые клиенту.
* Создайте класс модуля форматирования входных данных, если необходимо десериализировать данные, получаемые от клиента.
* Добавьте экземпляры модулей форматирования в коллекции `InputFormatters` и `OutputFormatters` в [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

В следующих разделах приводятся инструкции по выполнению каждого из этих действий с примерами кода.

## <a name="how-to-create-a-custom-formatter-class"></a>Создание пользовательского класса модуля форматирования

Чтобы создать модуль форматирования, выполните указанные ниже действия.

* Наследуйте класс от подходящего базового класса.
* Укажите в конструкторе допустимые типы передаваемых данных и кодировки.
* Переопределите методы `CanReadType`/`CanWriteType`.
* Переопределите методы `ReadRequestBodyAsync`/`WriteResponseBodyAsync`.
  
### <a name="derive-from-the-appropriate-base-class"></a>Наследование от подходящего базового класса

Для текстовых типов данных (например, vCard) произведите наследование от базового класса [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) или [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Для двоичных типов произведите наследование от базового класса [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) или [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Указание допустимых типов передаваемых данных и кодировок.

Укажите в конструкторе допустимые типы передаваемых данных и кодировки, добавив их в коллекции `SupportedMediaTypes` и `SupportedEncodings`.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Внедрение зависимостей конструктора в класс модуля форматирования невозможно. Например, невозможно получить средство ведения журнала, добавив соответствующий параметр в конструктор. Для доступа к службам необходимо использовать объект контекста, который передается в ваши методы. В приведенном [ниже](#read-write) примере кода показано, как это делается.

### <a name="override-canreadtypecanwritetype"></a>Переопределение методов CanReadType и CanWriteType

Укажите типы, в которые может осуществляться десериализация или из которых может осуществляться сериализация, переопределив метод `CanReadType` или `CanWriteType`. Например, текст в формате vCard может создаваться только из типа `Contact`, и наоборот.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Метод CanWriteResult

В некоторых ситуациях следует переопределять метод `CanWriteResult`, а не `CanWriteType`. Используйте `CanWriteResult`, если выполняются указанные ниже условия.

* Метод действия возвращает класс модели.
* Существуют производные классы, которые могут возвращаться во время выполнения.
* Во время выполнения необходимо знать, какой производный класс был возвращен действием.

Предположим, сигнатура метода действия возвращает тип `Person`, но может также возвращать типы `Student` и `Instructor`, производные от `Person`. Если модуль форматирования должен обрабатывать только объекты `Student`, проверьте тип свойства [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) в объекте контекста, предоставленном методу `CanWriteResult`. Обратите внимание на то, что если метод действия возвращает `IActionResult`, использовать `CanWriteResult` нет необходимости. В этом случае метод `CanWriteType` получает тип во время выполнения.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Переопределение методов ReadRequestBodyAsync и WriteResponseBodyAsync

Фактическая работа по десериализации и сериализации осуществляется в методе `ReadRequestBodyAsync` или `WriteResponseBodyAsync`. В выделенных строках ниже показано, как получить службы из контейнера внедрения зависимостей (получить их из параметров конструктора нельзя).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Пример форматировщика входных данных см. в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Настройка использования пользовательского модуля форматирования в MVC

Для использования пользовательского модуля форматирования добавьте экземпляр его класса в коллекцию `InputFormatters` или `OutputFormatters`.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Модули форматирования обрабатываются в порядке добавления. Первый модуль имеет приоритет.

## <a name="next-steps"></a>Дальнейшие действия

* [Пример приложения для этого документа](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), в котором реализуются простые форматировщики входных и выходных данных в формате vCard. Это приложение считывает и записывает карточки vCard, которые выглядят следующим образом:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Чтобы увидеть выходные данные vCard, запустите приложение и отправьте запрос Get с заголовком Accept "text/vcard" на адрес `http://localhost:63313/api/contacts/` (при запуске из Visual Studio) или `http://localhost:5000/api/contacts/` (при запуске из командной строки).

Чтобы добавить карточку vCard в коллекцию контактов в памяти, отправьте запрос Post на тот же URL-адрес. Запрос должен иметь заголовок Content-Type "text/vcard", а в его теле должен содержаться текст карточки vCard, отформатированный так, как показано выше.
