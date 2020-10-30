---
title: 'Пример SameSite ASP.NET Core 2,1 MVC :::no-loc(cookie):::'
author: rick-anderson
description: 'Пример SameSite ASP.NET Core 2,1 MVC :::no-loc(cookie):::'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051555"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="b8b47-103">Пример SameSite ASP.NET Core 2,1 MVC :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="b8b47-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="b8b47-104">ASP.NET Core 2,1 имеет встроенную поддержку атрибута [SameSite](https://www.owasp.org/index.php/SameSite) , но она была записана в исходный стандарт.</span><span class="sxs-lookup"><span data-stu-id="b8b47-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="b8b47-105">[Исправленное поведение](https://github.com/dotnet/aspnetcore/issues/8212) изменило значение параметра `SameSite.None` , чтобы выдать атрибут sameSite со значением `None` , а не выдавать значение вообще.</span><span class="sxs-lookup"><span data-stu-id="b8b47-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="b8b47-106">Если вы не хотите выпустить значение, можно задать `SameSite` для свойства значение :::no-loc(cookie)::: -1.</span><span class="sxs-lookup"><span data-stu-id="b8b47-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="b8b47-107">Написание атрибута SameSite</span><span class="sxs-lookup"><span data-stu-id="b8b47-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="b8b47-108">Ниже приведен пример того, как записать атрибут SameSite в :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="b8b47-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="b8b47-109">Настройка :::no-loc(Cookie)::: проверки подлинности и состояния сеанса :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="b8b47-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="b8b47-110">:::no-loc(Cookie)::: Проверка подлинности, состояние сеанса и [различные другие компоненты](../samesite.md?view=aspnetcore-2.1) задают свои параметры sameSite :::no-loc(Cookie)::: с помощью параметров, например</span><span class="sxs-lookup"><span data-stu-id="b8b47-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="b8b47-111">В приведенном выше коде как :::no-loc(cookie)::: Проверка подлинности, так и состояние сеанса устанавливают атрибут sameSite в, выдает `None` атрибут со `None` значением, а также присвойте атрибуту Secure значение true.</span><span class="sxs-lookup"><span data-stu-id="b8b47-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="b8b47-112">Запуск примера</span><span class="sxs-lookup"><span data-stu-id="b8b47-112">Run the sample</span></span>

<span data-ttu-id="b8b47-113">При запуске [примера проекта](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)Загрузите отладчик браузера на начальной странице и используйте его для просмотра :::no-loc(cookie)::: коллекции для сайта.</span><span class="sxs-lookup"><span data-stu-id="b8b47-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="b8b47-114">Чтобы сделать это в пограничных и Chrome, нажмите на `F12` `Application` вкладку и щелкните URL-адрес сайта в `:::no-loc(Cookie):::s` параметре в `Storage` разделе.</span><span class="sxs-lookup"><span data-stu-id="b8b47-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![Отладчик браузера::: No-Loc (cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="b8b47-116">На приведенном выше изображении можно увидеть, что :::no-loc(cookie)::: созданный примером при нажатии кнопки "создать SameSite :::no-loc(Cookie)::: " имеет значение атрибута SameSite `Lax` , совпадающее со значением, заданным в [образце кода](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="b8b47-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="b8b47-117">Перехват :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="b8b47-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="b8b47-118">Чтобы перехватить :::no-loc(cookie)::: , чтобы изменить значение None в соответствии с его поддержкой в агенте браузера пользователя, необходимо использовать по `:::no-loc(Cookie):::Policy` промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="b8b47-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="b8b47-119">Он должен быть помещен в конвейер HTTP-запросов **перед** всеми компонентами, которые записывают :::no-loc(cookie)::: и настраиваются в `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="b8b47-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="b8b47-120">Чтобы вставить его в конвейер, используйте `app.Use:::no-loc(Cookie):::Policy()` `Configure(IApplicationBuilder, IHostingEnvironment)` метод в [Startup.CS](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="b8b47-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="b8b47-121">Пример:</span><span class="sxs-lookup"><span data-stu-id="b8b47-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="b8b47-122">Затем в `ConfigureServices(IServiceCollection services)` :::no-loc(cookie)::: политике настройте политику для вызова вспомогательного класса, когда :::no-loc(cookie)::: добавляются или удаляются.</span><span class="sxs-lookup"><span data-stu-id="b8b47-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="b8b47-123">Пример:</span><span class="sxs-lookup"><span data-stu-id="b8b47-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="b8b47-124">Вспомогательная функция `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` :</span><span class="sxs-lookup"><span data-stu-id="b8b47-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="b8b47-125">Вызывается, когда :::no-loc(cookie)::: s добавляется к запросу или удаляется из запроса.</span><span class="sxs-lookup"><span data-stu-id="b8b47-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="b8b47-126">Проверяет, `SameSite` имеет ли свойство значение `None` .</span><span class="sxs-lookup"><span data-stu-id="b8b47-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="b8b47-127">Если параметр `SameSite` имеет значение `None` , а для текущего агента пользователя известно, что он не поддерживает значение атрибута None.</span><span class="sxs-lookup"><span data-stu-id="b8b47-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="b8b47-128">Проверка выполняется с помощью класса [самеситесуппорт](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="b8b47-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="b8b47-129">Задает, чтобы не выдавало `SameSite` значение, задав для свойства `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="b8b47-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="b8b47-130">Нацеливание на .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b8b47-130">Targeting .NET Framework</span></span>

<span data-ttu-id="b8b47-131">ASP.NET Core и System. Web (классическая модель ASP.NET) имеют независимые реализации SameSite.</span><span class="sxs-lookup"><span data-stu-id="b8b47-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="b8b47-132">Исправления SameSite KB для .NET Framework не требуются, если используется ASP.NET Core и не является требованием к версии System. Web SameSite минимальной платформы (.NET 4.7.2), применяемым к ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8b47-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="b8b47-133">ASP.NET Core в .NET требует обновления зависимостей пакетов NuGet для получения соответствующих исправлений.</span><span class="sxs-lookup"><span data-stu-id="b8b47-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="b8b47-134">Чтобы получить ASP.NET Core изменения для .NET Framework убедитесь в наличии прямой ссылки на исправленные пакеты и версии (2.1.14 или более поздней версии 2,1).</span><span class="sxs-lookup"><span data-stu-id="b8b47-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="b8b47-135">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="b8b47-135">More Information</span></span>
 
<span data-ttu-id="b8b47-136">[Обновления Chrome](https://www.chromium.org/updates/same-site) 
 [Документация по](../samesite.md?view=aspnetcore-2.1) 
 ASP.NET Core SameSite [ASP.NET Core 2,1 SameSite объявление об изменениях](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="b8b47-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>