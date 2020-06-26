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
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Перенос проверки подлинности и Identity в ASP.NET Core

Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)

В предыдущей статье мы [перенесли конфигурацию из проекта ASP.NET MVC в ASP.NET Core MVC](xref:migration/configuration). В этой статье мы переносим функции регистрации, входа и управления пользователями.

## <a name="configure-identity-and-membership"></a>Настройка Identity и членство

В ASP.NET MVC функции проверки подлинности и идентификации настраиваются с помощью ASP.NET Identity в *Startup.Auth.cs* и *IdentityConfig.CS*, расположенных в папке *App_Start* . В ASP.NET Core MVC эти функции настраиваются в *Startup.CS*.

Установите следующие пакеты NuGet:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

В *Startup.CS*обновите `Startup.ConfigureServices` метод, чтобы использовать Entity Framework и Identity службы:

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

На этом этапе существует два типа, упоминаемых в приведенном выше коде, которые еще не перенесены из проекта MVC ASP.NET: `ApplicationDbContext` и `ApplicationUser` . Создайте новую папку *Models* в проекте ASP.NET Core и добавьте в нее два класса, соответствующие этим типам. Версии ASP.NET MVC этих классов можно найти в */Моделс/идентитимоделс.КС*, но мы будем использовать по одному файлу для каждого класса в перенесенном проекте, так как это более ясно.

*ApplicationUser.CS*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.CS*:

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

ASP.NET Coreный веб-проект MVC не включает в себя значительную настройку пользователей или `ApplicationDbContext` . При переносе реального приложения необходимо также перенести все пользовательские свойства и методы пользователя и классов приложения, а также `DbContext` любые другие классы модели, используемые вашим приложением. Например, если у вас `DbContext` есть `DbSet<Album>` , необходимо перенести `Album` класс.

Используя эти файлы, можно выполнить компиляцию файла *Startup.CS* , обновив `using` инструкции:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Теперь наше приложение готово к поддержке проверки подлинности и Identity служб. Они просто должны предоставлять эти функции пользователям.

## <a name="migrate-registration-and-login-logic"></a>Миграция логики регистрации и входа в систему

Благодаря Identity службам, настроенным для доступа к приложениям и данным, настроенным с помощью Entity Framework и SQL Server, мы готовы к добавлению поддержки регистрации и входа в приложение. Вспомним, что [ранее в процессе миграции](xref:migration/mvc#migrate-the-layout-file) мы заносим ссылку на *_LoginPartial* в *_layout. cshtml*. Теперь пришло время вернуться к этому коду, раскомментировать его и добавить необходимые контроллеры и представления для поддержки функций входа.

Раскомментируйте `@Html.Partial` строку в *_layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Теперь добавьте новое представление с Razor именем *_LoginPartial* в папку *views/Shared* .

Обновите *_LoginPartial. cshtml* со следующим кодом (замените все его содержимое):

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

На этом этапе вы сможете обновить сайт в браузере.

## <a name="summary"></a>Сводка

ASP.NET Core вносит изменения в функции ASP.NET Identity . В этой статье вы узнали, как перенести функции проверки подлинности и управления пользователями ASP.NET Identity в ASP.NET Core.
