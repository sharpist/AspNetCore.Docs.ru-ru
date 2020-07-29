---
title: Новые возможности в ASP.NET Core 3.1
author: rick-anderson
description: Сведения о новых возможностях в ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: aspnetcore-3.1
ms.openlocfilehash: 92804d168381526100ddb8a368f71d201bd4cad9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407672"
---
# <a name="whats-new-in-aspnet-core-31"></a>Новые возможности в ASP.NET Core 3.1

В этой статье описываются наиболее важные изменения в ASP.NET Core 3.1 со ссылками на соответствующую документацию.

## <a name="partial-class-support-for-razor-components"></a>Поддержка разделяемых классов для компонентов [Razor

Компоненты [Razor теперь создаются как разделяемые классы. Код компонента [Razor можно написать как файл кода программной части, определенный как разделяемый класс, вместо того чтобы определять весь код компонента в одном файле. Дополнительные сведения см. в разделе [Поддержка разделяемых классов](xref:blazor/components/index#partial-class-support).

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Вспомогательная функция тега компонента [Blazor и передача параметров в компоненты верхнего уровня

В [Blazor с ASP.NET Core 3.0 компоненты отрисовывались как страницы и представления с помощью вспомогательной функции HTML (`Html.RenderComponentAsync`). В ASP.NET Core 3.1 компонент отрисовывается из страницы или представления с помощью новой вспомогательной функции тега компонента:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Вспомогательная функция HTML по-прежнему поддерживается в ASP.NET Core 3.1, но рекомендуется использовать вспомогательную функцию тега компонента.

Приложения [Blazor Server теперь могут передавать параметры в компоненты верхнего уровня во время первоначальной отрисовки. Ранее параметры можно было передавать в компонент верхнего уровня только с помощью [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). В этом выпуске поддерживается как [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server), так и [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered). Все указанные значения параметров сериализуются как JSON и включаются в исходный ответ.

Например, компонент `Counter` может предварительно отрисовываться со значением приращения (`IncrementAmount`):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Дополнительные сведения см. в разделе [Интеграция компонентов в [Razor Pages и приложения MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Поддержка общих очередей в HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) поддерживает создание анонимных очередей запросов. В ASP.NET Core 3.1 добавлена возможность создания именованной очереди запросов HTTP.sys или присоединения к существующей очереди. Создание именованной очереди запросов HTTP.sys или присоединение к ней обеспечивает сценарии, в которых процесс контроллера HTTP.Sys, которому принадлежит очередь, не зависит от процесса прослушивателя. Такая независимость позволяет сохранять существующие соединения и находящиеся в очереди запросы при перезапуске процесса прослушивателя:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Критические изменения для файлов cookie SameSite

Поведение файлов cookie SameSite изменилось в соответствии с предстоящими изменениями в браузерах. Это может повлиять на сценарии проверки подлинности, такие как AzureAd, OpenIdConnect или WsFederation. Для получения дополнительной информации см. <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a>Запрет выполнения действий по умолчанию для событий в приложениях [Blazor

Используйте атрибут директивы `@on{EVENT}:preventDefault`, чтобы предотвратить выполнение действия по умолчанию для события. В следующем примере запрещается действие по умолчанию, отображающее символ клавиши в текстовом поле:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Дополнительные сведения см. в разделе [Запрет действий по умолчанию](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-blazor-apps"></a>Остановка распространения событий в приложениях [Blazor

Используйте атрибут директивы `@on{EVENT}:stopPropagation`, чтобы остановить распространение событий. В следующем примере при установке флажка события щелчка мышью из дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Дополнительные сведения см. в разделе [Отключение распространения событий](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-blazor-app-development"></a>Подробные сведения об ошибках во время разработки приложений [Blazor

Если во время разработки приложение [Blazor работает неправильно, подробные сведения об ошибках в приложении могут помочь в устранении неполадок. При возникновении ошибки в приложении [Blazor в нижней части экрана отображается золотистая полоска.

* Во время разработки из этой полоски можно перейти в консоль браузера, где можно просмотреть исключение.
* В рабочей среде эта полоска уведомляет пользователя о том, что произошла ошибка, и рекомендует обновить содержимое окна браузера.

Дополнительные сведения см. в разделе [Подробные сведения об ошибках во время разработки](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
