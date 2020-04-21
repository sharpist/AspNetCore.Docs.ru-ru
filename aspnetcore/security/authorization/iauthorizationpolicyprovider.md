---
title: Поставщики политики пользовательских авторизов в ASP.NET Core
author: mjrousos
description: Узнайте, как использовать пользовательский IAuthorizationPolicyProvider в ASP.NET базовом приложении для динамического создания политик авторизации.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661773"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Поставщики политики пользовательских авторизаций, использующие IAuthorizationPolicyProvider в ASP.NET Core 

Автор: [Майк Роусос (Mike Rousos)](https://github.com/mjrousos)

Обычно при использовании [авторизации на основе политики](xref:security/authorization/policies)политики регистрируются путем вызова `AuthorizationOptions.AddPolicy` как части конфигурации службы авторизации. В некоторых сценариях может оказаться невозможным (или желательным) зарегистрировать все политики авторизации таким образом. В этих случаях можно использовать `IAuthorizationPolicyProvider` обычай контролировать способы управления политиками авторизации.

Примеры сценариев, где пользовательский [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) может быть полезным, включают:

* Использование внешней службы для оценки политики.
* Использование широкого спектра политик (например, для различных номеров или возрастов), поэтому нет смысла `AuthorizationOptions.AddPolicy` добавлять каждую индивидуальную политику авторизации с вызовом.
* Создание политик в режиме выполнения на основе информации во внешнем источнике данных (например, в базе данных) или динамические определения требований к авторизации с помощью другого механизма.

[Просмотр или загрузка примера кода](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) из [репозитория AspNetCore GitHub.](https://github.com/dotnet/AspNetCore) Загрузите файл репозивора dotnet/AspNetCore. Отстегните файл. Перейдите к папке проекта *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Настройка поиска политики

ASP.NET основные приложения используют `IAuthorizationPolicyProvider` реализацию интерфейса для получения политикавторизации. По умолчанию, [по умолчанию, По умолчанию, по умолчанию, по умолчанию,](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) зарегистрирован и используется. `DefaultAuthorizationPolicyProvider`возвращает политики `AuthorizationOptions` из предоставленных в вызове. `IServiceCollection.AddAuthorization`

Настройте это поведение, `IAuthorizationPolicyProvider` зарегистрировав другую реализацию в контейнере [для впрыска зависимости](xref:fundamentals/dependency-injection) приложения. 

Интерфейс `IAuthorizationPolicyProvider` содержит три AIS:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) возвращает политику авторизации для данного имени.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) возвращает политику авторизации по `[Authorize]` умолчанию (политика, используемая для атрибутов без указанной политики). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) возвращает политику авторизации резервного копирования (политика, используемая программой авторизации Middleware, когда политика не указана). 

Внедряя эти AAP, можно настроить способы предоставления политик авторизации.

## <a name="parameterized-authorize-attribute-example"></a>Параметризованный авторизованный пример атрибута

Одним из `IAuthorizationPolicyProvider` сценариев, `[Authorize]` где это полезно, является включение пользовательских атрибутов, требования которых зависят от параметра. Например, в [документации, основанной на разрешении](xref:security/authorization/policies) политики, в качестве выборки использовалась возрастная политика (AtLeast21). Если различные действия контроллера в приложении должны быть доступны пользователям *разных* возрастов, было бы полезно иметь много различных возрастных политик. Вместо регистрации всех различных возрастных политик, в `AuthorizationOptions`которые будет необходимо приложение, можно динамически генерировать политики с помощью пользовательского. `IAuthorizationPolicyProvider` Чтобы упростить использование политик, можно аннотировать действия с `[MinimumAgeAuthorize(20)]`помощью пользовательского атрибута авторизации.

## <a name="custom-authorization-attributes"></a>Атрибуты пользовательского авторизации

Политики авторизации определяются по их именам. Описанный `MinimumAgeAuthorizeAttribute` ранее обычай должен составить аргументы в строку, которая может быть использована для получения соответствующей политики авторизации. Вы можете сделать это, `AuthorizeAttribute` произвольно и сделать свойство `Age` обернуть собственности. `AuthorizeAttribute.Policy`

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

Этот тип атрибута имеет строку, основанную `Policy` `"MinimumAge"`на жесткой приставке () и рядом передается через конструктор.

Вы можете применить его к действиям `Authorize` так же, как и другие атрибуты, за исключением того, что он принимает целый ряд в качестве параметра.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Пользовательские IАвторизациюProvider

Обычай `MinimumAgeAuthorizeAttribute` упрощает запрос политикавторов для любого желаемого возраста. Следующая проблема заключается в обеспечении доступности политик авторизации для всех этих возрастов. Это где `IAuthorizationPolicyProvider` полезно.

При `MinimumAgeAuthorizationAttribute`использовании имен политики авторизации будут следовать шаблону, `"MinimumAge" + Age`поэтому пользовательский режим `IAuthorizationPolicyProvider` должен генерировать политики авторизации:

* Разбор возраста от имени политики.
* Использование `AuthorizationPolicyBuilder` для создания нового`AuthorizationPolicy`
* В этом и следующих примерах предполагается, что пользователь проходит аутентификации с помощью файла cookie. Конструкция `AuthorizationPolicyBuilder` должна быть построена с по крайней мере одним названием схемы авторизации или всегда успешно. В противном случае нет информации о том, как предоставить вызов пользователю и исключение будет брошено.
* Добавление требований к политике в `AuthorizationPolicyBuilder.AddRequirements`зависимости от возраста с . В других сценариях `RequireClaim`можно `RequireRole`использовать `RequireUserName` , или вместо этого.

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Несколько поставщиков политик авторизации

При использовании пользовательских `IAuthorizationPolicyProvider` реализаций имейте в виду, что ASP.NET Core использует только один `IAuthorizationPolicyProvider`экземпляр. Если пользовательская поставщик не в состоянии предоставить политики авторизации для всех имен политик, которые будут использоваться, он должен отложить на резервного поставщика. 

Например, рассмотрим приложение, которое нуждается как в пользовательских возрастных политиках, так и в более традиционных ролевых политиках поиска. Такое приложение может использовать поставщика пользовательских политик авторизации, который:

* Попытки разобрать имена политик. 
* Вызовы в другой поставщик `DefaultAuthorizationPolicyProvider`политик (например), если имя политики не содержит возраста.

Приведенная `IAuthorizationPolicyProvider` выше реализация примера может `DefaultAuthorizationPolicyProvider` быть обновлена для использования поставщика политики резервного копирования в своем конструкторе (для использования в случае, если имя политики не соответствует ожидаемому шаблону 'MinimumAge' и возраст).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Затем `GetPolicyAsync` метод можно обновить, чтобы `BackupPolicyProvider` использовать вместо возврата нулевых:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Политика по умолчанию

Помимо предоставления именных политик `IAuthorizationPolicyProvider` авторизации, `GetDefaultPolicyAsync` пользовательский способ `[Authorize]` должен реализовать для предоставления политики авторизации для атрибутов без указанного имени политики.

Во многих случаях этот атрибут авторизации требует только аутентифицированного пользователя, `RequireAuthenticatedUser`поэтому вы можете сделать необходимую политику с вызовом:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Как и все аспекты обычая, `IAuthorizationPolicyProvider`вы можете настроить это, по мере необходимости. В некоторых случаях может быть желательно получить политику по `IAuthorizationPolicyProvider`умолчанию из запасного варианта.

## <a name="fallback-policy"></a>Политика возврата

Пользовательский `IAuthorizationPolicyProvider` обычай `GetFallbackPolicyAsync` может дополнительно реализовать для предоставления политики, используемой при [объединении политик](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) и когда не указаны политики. При `GetFallbackPolicyAsync` возврате политики без нулевых возврат используется системой авторизации, когда для запроса не указаны политики.

Если не требуется политика резервного `null` копирования, поставщик может вернуться или отложить резервному поставщику:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Используйте пользовательский IАвторитиадтиВИ-провайдер

Для использования пользовательских `IAuthorizationPolicyProvider`политик из, вы должны:

* Зарегистрируйте `AuthorizationHandler` соответствующие типы с инъекцией зависимостей (описанными в [авторизации на основе политики),](xref:security/authorization/policies#authorization-handlers)как и во всех сценариях авторизации, основанных на политике.
* Заменить `IAuthorizationPolicyProvider` поставщика политики по умолчанию, зарегистрируйте пользовательский тип в коллекции служб инъекций зависимостей приложения.ru. `Startup.ConfigureServices`

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Полный `IAuthorizationPolicyProvider` пользовательский образец доступен в [репозитории dotnet/aspnetcore GitHub.](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)
