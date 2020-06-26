---
title: Авторизация на основе утверждений в ASP.NET Core
author: rick-anderson
description: Узнайте, как добавить проверки утверждений для авторизации в ASP.NET Core приложении.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: 404e26f0fb5e71dbc22b1c08a2f8caf8461ad7e1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406385"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="b49a3-103">Авторизация на основе утверждений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b49a3-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="b49a3-104">При создании удостоверения ему может быть назначено одно или несколько утверждений, выданных доверенной стороной.</span><span class="sxs-lookup"><span data-stu-id="b49a3-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="b49a3-105">Утверждение — это пара "имя-значение", которая представляет предметную тему, а не то, что может делать субъект.</span><span class="sxs-lookup"><span data-stu-id="b49a3-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="b49a3-106">Например, у вас может быть лицензия драйвера, выданная локальным центром лицензий.</span><span class="sxs-lookup"><span data-stu-id="b49a3-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="b49a3-107">У лицензии вашего драйвера есть Дата рождения.</span><span class="sxs-lookup"><span data-stu-id="b49a3-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="b49a3-108">В этом случае имя утверждения будет представлять собой `DateOfBirth` дату рождения, например, `8th June 1970` а издатель будет центром лицензий.</span><span class="sxs-lookup"><span data-stu-id="b49a3-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="b49a3-109">Авторизация на основе утверждений, в самом простом, проверяет значение утверждения и разрешает доступ к ресурсу на основе этого значения.</span><span class="sxs-lookup"><span data-stu-id="b49a3-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="b49a3-110">Например, если требуется доступ к ночному клубу, процесс авторизации может быть следующим:</span><span class="sxs-lookup"><span data-stu-id="b49a3-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="b49a3-111">Директор по безопасности дверцы вычислит значение даты утверждения о рождении и получит ли он доверие к издателю (Управление центром лицензий) перед предоставлением доступа.</span><span class="sxs-lookup"><span data-stu-id="b49a3-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="b49a3-112">Удостоверение может содержать несколько утверждений с несколькими значениями и может содержать несколько утверждений одного типа.</span><span class="sxs-lookup"><span data-stu-id="b49a3-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="b49a3-113">Добавление проверок утверждений</span><span class="sxs-lookup"><span data-stu-id="b49a3-113">Adding claims checks</span></span>

<span data-ttu-id="b49a3-114">Проверки авторизации на основе утверждений являются декларативными. разработчик внедряет их в код для контроллера или действия в пределах контроллера, указывая утверждения, которыми должен обладать текущий пользователь, и, при необходимости, значение, которое должно быть сопоставлено для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b49a3-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="b49a3-115">Требования к утверждениям основаны на политиках. разработчик должен создать и зарегистрировать политику, которая выражает требования к утверждениям.</span><span class="sxs-lookup"><span data-stu-id="b49a3-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="b49a3-116">Простейший тип политики утверждений ищет наличие утверждения и не проверяет его.</span><span class="sxs-lookup"><span data-stu-id="b49a3-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="b49a3-117">Сначала необходимо создать и зарегистрировать политику.</span><span class="sxs-lookup"><span data-stu-id="b49a3-117">First you need to build and register the policy.</span></span> <span data-ttu-id="b49a3-118">Это происходит как часть конфигурации службы авторизации, которая обычно принимает часть в `ConfigureServices()` файле *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="b49a3-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="b49a3-119">В этом случае `EmployeeOnly` политика проверяет наличие `EmployeeNumber` утверждения на текущее удостоверение.</span><span class="sxs-lookup"><span data-stu-id="b49a3-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="b49a3-120">Затем примените политику, используя `Policy` свойство `AuthorizeAttribute` атрибута, чтобы указать имя политики.</span><span class="sxs-lookup"><span data-stu-id="b49a3-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="b49a3-121">`AuthorizeAttribute`Атрибут может быть применен ко всему контроллеру. в этом экземпляре будет разрешен доступ к любому действию на контроллере только в удостоверениях, соответствующих политике.</span><span class="sxs-lookup"><span data-stu-id="b49a3-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="b49a3-122">Если у вас есть контроллер, защищенный `AuthorizeAttribute` атрибутом, но хотите разрешить анонимный доступ к определенным действиям, примените `AllowAnonymousAttribute` атрибут.</span><span class="sxs-lookup"><span data-stu-id="b49a3-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="b49a3-123">Большинство заявок поставляются со значением.</span><span class="sxs-lookup"><span data-stu-id="b49a3-123">Most claims come with a value.</span></span> <span data-ttu-id="b49a3-124">При создании политики можно указать список допустимых значений.</span><span class="sxs-lookup"><span data-stu-id="b49a3-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="b49a3-125">Следующий пример будет выполнен только для сотрудников, чей номер сотрудника был 1, 2, 3, 4 или 5.</span><span class="sxs-lookup"><span data-stu-id="b49a3-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="b49a3-126">Добавление проверки универсального утверждения</span><span class="sxs-lookup"><span data-stu-id="b49a3-126">Add a generic claim check</span></span>

<span data-ttu-id="b49a3-127">Если значение утверждения не является одним значением или требуется преобразование, используйте [рекуиреассертион](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="b49a3-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="b49a3-128">Дополнительные сведения см. в разделе [Использование инструкции Func для выполнения политики](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="b49a3-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="b49a3-129">Оценка нескольких политик</span><span class="sxs-lookup"><span data-stu-id="b49a3-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="b49a3-130">При применении нескольких политик к контроллеру или действию все политики должны пройти до предоставления доступа.</span><span class="sxs-lookup"><span data-stu-id="b49a3-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="b49a3-131">Пример.</span><span class="sxs-lookup"><span data-stu-id="b49a3-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="b49a3-132">В приведенном выше примере любое удостоверение, удовлетворяющее `EmployeeOnly` политике, может получить доступ к `Payslip` действию, так как эта политика применяется на контроллере.</span><span class="sxs-lookup"><span data-stu-id="b49a3-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="b49a3-133">Однако для вызова `UpdateSalary` действия удостоверение должно удовлетворять *both* `EmployeeOnly` политике и `HumanResources` политике.</span><span class="sxs-lookup"><span data-stu-id="b49a3-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="b49a3-134">Если вы хотите использовать более сложные политики, например, выполнив утверждение о рождении, вычисляя возраст из него, а затем проверив возраст на 21 или более раннюю версию, необходимо написать [пользовательские обработчики политик](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="b49a3-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
