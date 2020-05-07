---
title: Изучение методов Details и Delete в приложении ASP.NET Core
author: rick-anderson
description: Узнайте о методе и представлении контроллера Details в базовом приложении ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: cff8bc0d3506210879974f711a4e665c8549051d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777558"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Изучение методов Details и Delete в приложении ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT)

Откройте контроллер Movie и изучите метод `Details`:

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Подсистема формирования шаблонов MVC, созданная этим методом действия, добавляет комментарий, показывающий HTTP-запрос, который вызывает метод. Здесь это запрос GET, состоящий из трех сегментов URL-адреса, контроллера `Movies`, метода `Details` и значения `id`. Эти сегменты определены в *Startup.cs*.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF упрощает поиск данных с помощью метода `FirstOrDefaultAsync`. Важной функцией обеспечения безопасности, встроенной в метод, является то, что код проверяет, что метод поиска обнаружил фильм до выполнения с ним каких-либо действий. Например, злоумышленник может внести ошибки на сайт путем изменения созданного ссылками URL-адреса с `http://localhost:{PORT}/Movies/Details/1` на что-то вроде `http://localhost:{PORT}/Movies/Details/12345` (или любое другое значение, которое не представляет фактический фильм). Если вы не проверили наличие фильма со значением NULL, приложение выдаст исключение.

Просмотрите методы `Delete` и `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Обратите внимание, что метод `HTTP GET Delete` не удаляет указанный фильм, он возвращает представление фильма, где можно выполнить (HttpPost) удаление. Выполнение операции удаления в ответ на запрос GET (или выполнение операции редактирования, создания или любой другой операции, изменяющей данные) открывает брешь в системе безопасности.

Метод `[HttpPost]`, который удаляет данные, называется `DeleteConfirmed`, поэтому метод HTTP POST обладает уникальной сигнатурой или именем. Ниже приведены сигнатуры двух методов:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Требуется, чтобы в среде CLR перегруженные методы имели уникальную сигнатуру параметров (то же имя метода, но другой список параметров). Однако здесь необходимы два метода `Delete` — один для GET и один для POST — с одинаковой сигнатурой параметров. (Они оба должны принимать целочисленное значение в качестве параметра.)

Существует два подхода к решению этой проблемы, один из которых заключается в указании разных имен для методов. Именно это было представлено в предыдущем примере механизма формирования шаблонов. Однако в этом случае возникает небольшая проблема: ASP.NET сопоставляет сегменты URL-адреса с методами действий по имени, а при переименовании метода, как правило, маршрутизация не сможет найти этот метод. Решение показано в примере, а именно: в метод `ActionName("Delete")` следует добавить атрибут `DeleteConfirmed`. Этот атрибут выполняет сопоставление для системы маршрутизации, чтобы URL-адрес, включающий /Delete/ для запроса POST, смог найти метод `DeleteConfirmed`.

Другим распространенным решением проблемы для методов с одинаковыми именами и сигнатурами является искусственное изменение сигнатуры метода POST для включения дополнительного (неиспользуемого) параметра. Именно это было сделано ранее, когда был добавлен параметр `notUsed`. То же самое можно сделать для метода `[HttpPost] Delete`:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Публикация в Azure

Сведения о развертывании в Azure см. в статье [Руководство. Создание веб-приложения .NET Core с базой данных SQL в службе приложений Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Назад](validation.md)
