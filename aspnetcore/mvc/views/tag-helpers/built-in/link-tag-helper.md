---
title: Вспомогательная функция тега ссылки в ASP.NET Core
author: rick-anderson
ms.author: riande
description: Обнаруживайте атрибуты вспомогательной функции тега ссылки ASP.NET Core и роль, которую играет каждый атрибут в расширении поведения тега ссылки HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059121"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега ссылки в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

[Вспомогательная функция тега ссылки](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) создает ссылку на первичный или резервный файл CSS. Обычно первичный файл CSS находится в [сети доставки содержимого](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Вспомогательная функция тега ссылки позволяет указать CDN для файла CSS и резервную копию, если CDN недоступен. Вспомогательная функция тега ссылки обеспечивает высокую производительность CDN с надежностью локального размещения.

В следующей Razor разметке показан `head` элемент файла макета, созданного с помощью шаблона веб-приложения ASP.NET Core.

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Ниже приведен код HTML из предыдущего кода (в среде, не являющейся средой разработки):

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

В приведенном выше коде вспомогательная функция тега ссылки создала элемент `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` и следующий код JavaScript, который используется для проверки наличия запрошенного файла *bootstrap.min.css* в сети CDN. В этом случае файл CSS был доступен, поэтому вспомогательная функция тега создала элемент `<link />` с файлом CSS CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Часто используемые атрибуты вспомогательной функции тега ссылки

Все атрибуты, свойства и методы вспомогательной функции тега ссылки см. в разделе [LinkTagHelper Class](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) (Класс LinkTagHelper).

### <a name="href"></a>href

Предпочтительный адрес связанного ресурса. Адрес передается в созданный код HTML во всех случаях.

### <a name="asp-fallback-href"></a>asp-fallback-href

URL-адрес таблицы стилей CSS, к которой можно перейти в случае сбоя основного URL-адреса.

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

Имя класса, определенное в таблице стилей для использования в качестве теста резервного экземпляра. Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

Имя свойства CSS, используемое для теста резервного экземпляра. Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

Значение свойства CSS, используемое для теста резервного экземпляра. Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
