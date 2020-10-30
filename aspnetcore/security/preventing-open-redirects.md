---
title: Предотвращение атак с открытым перенаправлением в ASP.NET Core
author: ardalis
description: Показано, как предотвратить атаки с открытым перенаправлением для ASP.NET Core приложения.
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: e546cd852367921c7c694db3639f7a233f606e75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058393"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="a96b7-103">Предотвращение атак с открытым перенаправлением в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a96b7-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="a96b7-104">Веб-приложение, которое перенаправляет на URL-адрес, указанный через запрос, например строку запроса или данные формы, потенциально может быть изменено для перенаправления пользователей на внешний вредоносный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="a96b7-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="a96b7-105">Такое незаконное изменение называется атакой с открытым перенаправлением.</span><span class="sxs-lookup"><span data-stu-id="a96b7-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="a96b7-106">Каждый раз, когда логика приложения перенаправляется на указанный URL-адрес, необходимо убедиться, что URL-адрес перенаправления не был изменен.</span><span class="sxs-lookup"><span data-stu-id="a96b7-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="a96b7-107">ASP.NET Core имеет встроенные функции, помогающие защитить приложения от атак с открытым перенаправлением (также называемых перенаправлением открытия).</span><span class="sxs-lookup"><span data-stu-id="a96b7-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="a96b7-108">Что такое атака с открытым перенаправлением?</span><span class="sxs-lookup"><span data-stu-id="a96b7-108">What is an open redirect attack?</span></span>

<span data-ttu-id="a96b7-109">Веб-приложения часто перенаправляют пользователей на страницу входа, когда они обращаются к ресурсам, требующим проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a96b7-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="a96b7-110">Перенаправление обычно включает `returnUrl` параметр QueryString, чтобы пользователь мог вернуться к первоначально запрошенному URL-адресу после успешного входа.</span><span class="sxs-lookup"><span data-stu-id="a96b7-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="a96b7-111">После проверки подлинности пользователь перенаправляется на URL-адрес, по которому они были изначально запрошены.</span><span class="sxs-lookup"><span data-stu-id="a96b7-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="a96b7-112">Так как URL-адрес назначения указан в запросе QueryString, злоумышленник может изменить строку запроса.</span><span class="sxs-lookup"><span data-stu-id="a96b7-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="a96b7-113">Неизмененная строка запроса может позволить сайту перенаправить пользователя на внешний вредоносный сайт.</span><span class="sxs-lookup"><span data-stu-id="a96b7-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="a96b7-114">Этот метод называется атакой с открытым перенаправлением (или перенаправлением).</span><span class="sxs-lookup"><span data-stu-id="a96b7-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="a96b7-115">Пример атаки</span><span class="sxs-lookup"><span data-stu-id="a96b7-115">An example attack</span></span>

<span data-ttu-id="a96b7-116">Злонамеренный пользователь может разработать атаку, предназначенную для предоставления злонамеренному пользователю доступа к учетным данным пользователя или конфиденциальной информации.</span><span class="sxs-lookup"><span data-stu-id="a96b7-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="a96b7-117">Чтобы начать атаку, злоумышленник убедится в том, что пользователь должен щелкнуть ссылку на страницу входа вашего сайта со `returnUrl` значением строки запроса, добавленным к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="a96b7-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="a96b7-118">Например, рассмотрим приложение в `contoso.com` , содержащее страницу входа в `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="a96b7-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="a96b7-119">Атака выполняется следующим образом.</span><span class="sxs-lookup"><span data-stu-id="a96b7-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="a96b7-120">Пользователь щелкает вредоносную ссылку `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (второй URL-адрес — contoso **1** . com, а не "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="a96b7-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso **1** .com", not "contoso.com").</span></span>
2. <span data-ttu-id="a96b7-121">Пользователь успешно входит в систему.</span><span class="sxs-lookup"><span data-stu-id="a96b7-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="a96b7-122">Пользователь перенаправляет (сайт) на `http://contoso1.com/Account/LogOn` (вредоносный сайт, который выглядит точно так же, как и реальный сайт).</span><span class="sxs-lookup"><span data-stu-id="a96b7-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="a96b7-123">Пользователь снова входит в систему (выдает вредоносный сайт с учетными данными) и перенаправляется обратно на реальный сайт.</span><span class="sxs-lookup"><span data-stu-id="a96b7-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="a96b7-124">Вероятно, пользователь считает, что первая попытка входа завершилась неудачно, а вторая попытка прошла успешно.</span><span class="sxs-lookup"><span data-stu-id="a96b7-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="a96b7-125">Скорее всего, пользователь не знает, что их учетные данные скомпрометированы.</span><span class="sxs-lookup"><span data-stu-id="a96b7-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Открытие процесса атаки с перенаправлением](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="a96b7-127">В дополнение к страницам входа некоторые сайты предоставляют страницы перенаправления или конечные точки.</span><span class="sxs-lookup"><span data-stu-id="a96b7-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="a96b7-128">Представьте, что приложение содержит страницу с открытым перенаправлением `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="a96b7-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="a96b7-129">Злоумышленник может создать, например, ссылку в сообщении электронной почты, которое переходит к `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="a96b7-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="a96b7-130">Обычный пользователь будет просматривать URL-адрес и видеть, что он начинается с имени вашего сайта.</span><span class="sxs-lookup"><span data-stu-id="a96b7-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="a96b7-131">Если вы доверяете этому, они будут щелкать ссылку.</span><span class="sxs-lookup"><span data-stu-id="a96b7-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="a96b7-132">Затем с помощью перенаправления будет отправлен пользователь на фишинговый сайт, который выглядит так же, как и ваш сайт.</span><span class="sxs-lookup"><span data-stu-id="a96b7-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="a96b7-133">Защита от атак с открытым перенаправлением</span><span class="sxs-lookup"><span data-stu-id="a96b7-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="a96b7-134">При разработке веб-приложений рассматривайте все предоставленные пользователем данные как ненадежные.</span><span class="sxs-lookup"><span data-stu-id="a96b7-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="a96b7-135">Если в приложении есть функциональные возможности, которые перенаправляют пользователя на основе содержимого URL-адреса, убедитесь, что такие перенаправления выполняются локально в приложении (или на известном URL-адресе, а не в URL-адресе, который может быть предоставлен в строке запроса).</span><span class="sxs-lookup"><span data-stu-id="a96b7-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="a96b7-136">локалредирект</span><span class="sxs-lookup"><span data-stu-id="a96b7-136">LocalRedirect</span></span>

<span data-ttu-id="a96b7-137">Используйте `LocalRedirect` вспомогательный метод из базового `Controller` класса:</span><span class="sxs-lookup"><span data-stu-id="a96b7-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="a96b7-138">`LocalRedirect` выдаст исключение, если указан нелокальный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="a96b7-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="a96b7-139">В противном случае он ведет себя так же, как и `Redirect` метод.</span><span class="sxs-lookup"><span data-stu-id="a96b7-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="a96b7-140">ислокалурл</span><span class="sxs-lookup"><span data-stu-id="a96b7-140">IsLocalUrl</span></span>

<span data-ttu-id="a96b7-141">Используйте метод [ислокалурл](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) для проверки URL-адресов перед перенаправлением:</span><span class="sxs-lookup"><span data-stu-id="a96b7-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="a96b7-142">В следующем примере показано, как проверить, является ли URL-адрес локальным, перед перенаправлением.</span><span class="sxs-lookup"><span data-stu-id="a96b7-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="a96b7-143">`IsLocalUrl`Метод защищает пользователей от случайного перенаправления к вредоносному сайту.</span><span class="sxs-lookup"><span data-stu-id="a96b7-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="a96b7-144">Вы можете записать в журнал сведения о URL-адресе, который был предоставлен, если нелокальный URL-адрес указан в ситуации, когда ожидался локальный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="a96b7-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="a96b7-145">URL-адреса перенаправления журнала могут помочь при диагностике атак перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a96b7-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
