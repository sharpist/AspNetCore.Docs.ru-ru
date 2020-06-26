---
title: Перенос проверки подлинности и Identity в ASP.NET Core
author: ardalis
description: Узнайте, как перенести проверку подлинности и удостоверение из проекта ASP.NET MVC в проект ASP.NET Core MVC.
ms.author: riande
ms.date: 3/22/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/identity
ms.openlocfilehash: 995de894bc77c4db5e5683b36e691b0c5a3463d3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403759"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="be55f-103">Перенос проверки подлинности и Identity в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be55f-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="be55f-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="be55f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="be55f-105">В предыдущей статье мы [перенесли конфигурацию из проекта ASP.NET MVC в ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="be55f-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="be55f-106">В этой статье мы переносим функции регистрации, входа и управления пользователями.</span><span class="sxs-lookup"><span data-stu-id="be55f-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="be55f-107">Настройка Identity и членство</span><span class="sxs-lookup"><span data-stu-id="be55f-107">Configure Identity and Membership</span></span>

<span data-ttu-id="be55f-108">В ASP.NET MVC функции проверки подлинности и идентификации настраиваются с помощью ASP.NET Identity в *Startup.Auth.cs* и *IdentityConfig.CS*, расположенных в папке *App_Start* .</span><span class="sxs-lookup"><span data-stu-id="be55f-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="be55f-109">В ASP.NET Core MVC эти функции настраиваются в *Startup.CS*.</span><span class="sxs-lookup"><span data-stu-id="be55f-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="be55f-110">Установите следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="be55f-110">Install the the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="be55f-111">В *Startup.CS*обновите `Startup.ConfigureServices` метод, чтобы использовать Entity Framework и Identity службы:</span><span class="sxs-lookup"><span data-stu-id="be55f-111">In *Startup.cs*, update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="be55f-112">На этом этапе существует два типа, упоминаемых в приведенном выше коде, которые еще не перенесены из проекта MVC ASP.NET: `ApplicationDbContext` и `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="be55f-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="be55f-113">Создайте новую папку *Models* в проекте ASP.NET Core и добавьте в нее два класса, соответствующие этим типам.</span><span class="sxs-lookup"><span data-stu-id="be55f-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="be55f-114">Версии ASP.NET MVC этих классов можно найти в */Моделс/идентитимоделс.КС*, но мы будем использовать по одному файлу для каждого класса в перенесенном проекте, так как это более ясно.</span><span class="sxs-lookup"><span data-stu-id="be55f-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="be55f-115">*ApplicationUser.CS*:</span><span class="sxs-lookup"><span data-stu-id="be55f-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="be55f-116">*ApplicationDbContext.CS*:</span><span class="sxs-lookup"><span data-stu-id="be55f-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="be55f-117">ASP.NET Coreный веб-проект MVC не включает в себя значительную настройку пользователей или `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="be55f-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="be55f-118">При переносе реального приложения необходимо также перенести все пользовательские свойства и методы пользователя и классов приложения, а также `DbContext` любые другие классы модели, используемые вашим приложением.</span><span class="sxs-lookup"><span data-stu-id="be55f-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="be55f-119">Например, если у вас `DbContext` есть `DbSet<Album>` , необходимо перенести `Album` класс.</span><span class="sxs-lookup"><span data-stu-id="be55f-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="be55f-120">Используя эти файлы, можно выполнить компиляцию файла *Startup.CS* , обновив `using` инструкции:</span><span class="sxs-lookup"><span data-stu-id="be55f-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="be55f-121">Теперь наше приложение готово к поддержке проверки подлинности и Identity служб.</span><span class="sxs-lookup"><span data-stu-id="be55f-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="be55f-122">Они просто должны предоставлять эти функции пользователям.</span><span class="sxs-lookup"><span data-stu-id="be55f-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="be55f-123">Миграция логики регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="be55f-123">Migrate registration and login logic</span></span>

<span data-ttu-id="be55f-124">Благодаря Identity службам, настроенным для доступа к приложениям и данным, настроенным с помощью Entity Framework и SQL Server, мы готовы к добавлению поддержки регистрации и входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="be55f-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="be55f-125">Вспомним, что [ранее в процессе миграции](xref:migration/mvc#migrate-the-layout-file) мы заносим ссылку на *_LoginPartial* в *_layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="be55f-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="be55f-126">Теперь пришло время вернуться к этому коду, раскомментировать его и добавить необходимые контроллеры и представления для поддержки функций входа.</span><span class="sxs-lookup"><span data-stu-id="be55f-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="be55f-127">Раскомментируйте `@Html.Partial` строку в *_layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="be55f-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="be55f-128">Теперь добавьте новое представление с Razor именем *_LoginPartial* в папку *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="be55f-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="be55f-129">Обновите *_LoginPartial. cshtml* со следующим кодом (замените все его содержимое):</span><span class="sxs-lookup"><span data-stu-id="be55f-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

<span data-ttu-id="be55f-130">На этом этапе вы сможете обновить сайт в браузере.</span><span class="sxs-lookup"><span data-stu-id="be55f-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="be55f-131">Сводка</span><span class="sxs-lookup"><span data-stu-id="be55f-131">Summary</span></span>

<span data-ttu-id="be55f-132">ASP.NET Core вносит изменения в функции ASP.NET Identity .</span><span class="sxs-lookup"><span data-stu-id="be55f-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="be55f-133">В этой статье вы узнали, как перенести функции проверки подлинности и управления пользователями ASP.NET Identity в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be55f-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
