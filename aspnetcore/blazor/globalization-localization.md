---
title: Глобализация и локализация в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как сделать компоненты Razor доступными для пользователей с различными языковыми и региональными параметрами.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772495"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>Глобализация и локализация в ASP.NET Core Blazor

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Компоненты Razor можно сделать доступными для пользователей с различными языковыми и региональными параметрами. Доступны следующие сценарии глобализации и локализации .NET:

* система ресурсов NET;
* форматирование чисел и дат в зависимости от языка и региональных параметров.

В настоящее время поддерживается ограниченный набор сценариев локализации ASP.NET Core:

* `IStringLocalizer<>` *поддерживается* в приложениях Blazor.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` и локализация заметок к данным являются сценариями MVC ASP.NET Core и **не поддерживается** в приложениях Blazor.

Для получения дополнительной информации см. <xref:fundamentals/localization>.

## <a name="globalization"></a>Глобализация

Функция `@bind` в Blazor выполняет форматирование и синтаксический анализ значений, отображаемых на основе текущего языка и региональных параметров пользователя.

Доступ к текущему языку и региональным параметрам можно получить из свойства <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) используется для следующих типов полей (`<input type="{TYPE}" />`):

* `date`
* `number`

Предыдущие типы полей:

* отображаются с использованием соответствующих правил форматирования браузера;
* не могут содержать текст в свободной форме;
* предоставляют характеристики взаимодействия с пользователем в зависимости от реализации браузера.

Следующие типы полей имеют особые требования к форматированию, которые в настоящее время не поддерживаются в Blazor, так как они не поддерживаются всеми основными браузерами.

* `datetime-local`
* `month`
* `week`

`@bind` поддерживает параметр `@bind:culture` для предоставления <xref:System.Globalization.CultureInfo?displayProperty=fullName> для анализа и форматирования значения. Указание языка и региональных параметров не рекомендуется при использовании типов полей `date` и `number`. Типы `date` и `number` имеют встроенную поддержку Blazor, которая обеспечивает поддержку языка и региональных параметров.

## <a name="localization"></a>Локализация

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Приложения Blazor WebAssembly задают язык и региональные параметры с помощью [предпочитаемого языка пользователя](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

Чтобы явно настроить язык и региональные параметры, задайте `CultureInfo.DefaultThreadCurrentCulture` и `CultureInfo.DefaultThreadCurrentUICulture` в `Program.Main`.

По умолчанию конфигурация компоновщика Blazor для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов. Дополнительные сведения и рекомендации по управлению поведением компоновщика см. в разделе <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

Хотя язык и региональные параметры, выбираемые Blazor по умолчанию, могут быть достаточными для большинства пользователей, рекомендуется предоставить пользователям возможность указать предпочитаемый языковой стандарт. Пример приложения Blazor WebAssembly с выбором языка и региональных параметров см. в описании примера приложения локализации [LocSample](https://github.com/pranavkm/LocSample).

### <a name="blazor-server"></a>Сервер Blazor

Приложения Blazor Server локализуются с помощью [промежуточного слоя локализации](xref:fundamentals/localization#localization-middleware). ПО промежуточного слоя выбирает соответствующие языки и региональные параметры для пользователей, запрашивающих ресурсы из приложения.

Язык и региональные параметры можно задать с помощью одного из следующих подходов.

* [Файлы "cookie"](#cookies)
* [Предоставление пользовательского интерфейса для выбора языка и региональных параметров](#provide-ui-to-choose-the-culture)

Дополнительные сведения и примеры см. в разделе <xref:fundamentals/localization>.

#### <a name="cookies"></a>Файлы cookie

Файл cookie локализации может запоминать язык и региональные параметры пользователя. Файл cookie создается методом `OnGet` на странице host приложения (*Pages/Host.cshtml.cs*). ПО промежуточного слоя локализации считывает файл cookie при последующих запросах, чтобы задать язык и региональные параметры пользователя. 

Использование файла cookie гарантирует, что соединение WebSocket сможет правильно распространить языковые и региональные параметры. Если схема локализации основана на URL-адресе или строке запроса, она может не поддерживать работу с WebSockets и не сможет сохранить язык и региональные параметры. Поэтому рекомендуемым подходом для локализации является использование файла cookie языка и региональных параметров.

Если язык и региональные параметры сохраняются в файле cookie с локализацией, то можно использовать любой метод для назначения языка и региональных параметров. Если у приложения уже есть установленная схема локализации для ASP.NET Core на стороне сервера, продолжайте использовать существующую инфраструктуру локализации приложения и задавайте файл cookie языка и региональных параметров для локализации в схеме приложения.

В следующем примере показано, как задать текущий язык и региональные параметры в файле cookie, который можно прочитать в ПО промежуточного слоя локализации. Создайте файл *Pages/_Host.cshtml.cs* со следующим содержимым в приложении Blazor Server:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Локализация обрабатывается приложением в следующей последовательности событий.

1. Браузер отправляет первоначальный HTTP-запрос в приложение.
1. Язык и региональные параметры назначаются в ПО промежуточного слоя локализации.
1. Метод `OnGet` в файле *_Host.cshtml.cs* сохраняет язык и региональные параметры в файле cookie как часть ответа.
1. Браузер открывает соединение WebSocket для создания интерактивного сеанса Blazor Server.
1. ПО промежуточного слоя для локализации считывает файл cookie и назначает язык и региональные параметры.
1. Сеанс Blazor Server начинается с нужными языком и региональными параметрами.

#### <a name="provide-ui-to-choose-the-culture"></a>Предоставление пользовательского интерфейса для выбора языка и региональных параметров

Чтобы предоставить пользовательский интерфейс, позволяющий пользователю выбрать язык и региональные параметры, рекомендуется *подход на основе перенаправления*. Процесс аналогичен тому, что происходит в веб-приложении, когда пользователь пытается получить доступ к защищенному ресурсу. Пользователь перенаправляется на страницу входа, а затем обратно к исходному ресурсу. 

Приложение сохраняет выбранный язык и региональные параметры пользователя с помощью перенаправления к контроллеру. Контроллер устанавливает выбранный язык и региональные параметры пользователя в файл cookie и перенаправляет пользователя обратно в исходный URI.

Установите конечную точку HTTP на сервере, чтобы задать выбранный язык и региональные параметры пользователя в файле cookie, и выполните перенаправление обратно в исходный URI:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Для предотвращения атак с открытым перенаправлением используйте результат действия `LocalRedirect`. Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.

В следующем компоненте показан пример выполнения начального перенаправления, когда пользователь выбирает язык и региональные параметры:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/localization>
