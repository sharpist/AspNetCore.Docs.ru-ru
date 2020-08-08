---
title: Совместимая версия для ASP.NET Core MVC
author: rick-anderson
description: Сведения о том, как класс Startup в ASP.NET Core настраивает службы и конвейер запросов приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/compatibility-version
ms.openlocfilehash: 3e705a43b263fcb0867f096e6062023ce10d5ad8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019876"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Совместимая версия для ASP.NET Core MVC

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"

Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> не предназначен для приложений ASP.NET Core 3.0. То есть вызов `SetCompatibilityVersion` с любым значением <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> не влияет на приложение.

* Следующая дополнительная версия ASP.NET Core может предоставить новое значение `CompatibilityVersion`.
* Значения `CompatibilityVersion` от `Version_2_0` до `Version_2_2` помечаются как `[Obsolete(...)]`.
* См. статью [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387) (Критические изменения API в областях борьбы с фальсификацией, CORS, диагностики, MVC и маршрутизации). Этот список содержит критические изменения для параметров совместимости.

Чтобы узнать, как `SetCompatibilityVersion` работает с приложениями ASP.NET Core 2.x, выберите [версию этой статьи для ASP.NET Core 2.2](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> позволяет приложению ASP.NET Core 2.x принимать или отклонять потенциально критические изменения в поведении, появившиеся в ASP.NET Core MVC 2.1 или 2.2. Эти потенциально критические изменения обычно затрагивают поведение подсистем MVC и способы вызова **кода** средой выполнения. Если принять эти изменения, вы сможете использовать актуальное поведение, которое сохранится в ASP.NET Core.

Следующий код задает режим совместимости в ASP.NET Core 2.2:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Мы рекомендуем протестировать приложение с помощью последней версии (`CompatibilityVersion.Latest`). Мы полагаем, что в большинстве приложений использование последней версии не приведет к критически важным изменениям в поведении.

Приложения, вызывающие `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`, защищены от потенциальных критически важных изменений в поведении, появившихся в версиях MVC ASP.NET Core 2.1/2.2. Особенности защиты:

* Не применяется ко всем изменениям в версии 2.1 и более поздних версиях. Она направлена только на потенциально критические изменения в поведении среды выполнения ASP.NET Core в подсистеме MVC.
* Не распространяется на ASP.NET Core 3.0.

По умолчанию для приложений ASP.NET Core 2.1 и 2.2, которые **не** вызывают `SetCompatibilityVersion`, предусмотрена совместимость с 2.0. Отсутствие вызова `SetCompatibilityVersion` равноценно вызову `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

Следующий код задает режим совместимости в ASP.NET Core 2.2, за исключением следующего поведения:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

Если в приложении возникают критические изменения в поведении, использование параметров совместимости имеет следующие преимущества:

* Позволяет использовать последний выпуск и отказаться от конкретных критических изменений.
* Дает вам время обновить приложение, чтобы оно работало с последними изменениями.

В документации по <xref:Microsoft.AspNetCore.Mvc.MvcOptions> подробно объясняется, что изменилось и почему изменения выгодны для большинства пользователей.

Для ASP.NET Core 3.0 старые варианты поведения, поддерживаемые параметрами совместимости, были удалены. Мы думаем, что это полезные изменения почти для всех пользователей. С внедрением этих изменений в 2.1 и 2.2 большинство приложений могут сразу воспользоваться преимуществами, в то время как для обновления других требуется время.
::: moniker-end
