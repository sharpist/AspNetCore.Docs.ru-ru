---
title: Устранение неполадок локализации в ASP.NET Core
author: hishamco
description: Сведения о диагностике проблем локализации в приложениях ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: f5c2be93be4f896b1822bf93deef24f091e30442
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774292"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Устранение неполадок локализации в ASP.NET Core

Автор: [Хишам Бин Атея](https://github.com/hishamco) (Hisham Bin Ateya)

В этой статье приводятся инструкции по диагностике проблем локализации в приложениях ASP.NET Core.

## <a name="localization-configuration-issues"></a>Проблемы с конфигурацией локализации

**Порядок ПО промежуточного слоя локализации**  
Локализация приложения может быть невозможна из-за неправильно упорядоченного ПО промежуточного слоя локализации.

Чтобы устранить эту проблему, убедитесь, что ПО промежуточного слоя локализации зарегистрировано до ПО промежуточного слоя MVC. В противном случае ПО промежуточного слоя локализации не будет применено.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Путь к ресурсам локализации не найден**

**Поддерживаемые языки и региональные параметры в RequestCultureProvider не совпадают с зарегистрированными**  

## <a name="resource-file-naming-issues"></a>Проблемы именования файлов ресурсов

В ASP.NET Core существуют предопределенные правила и рекомендации для именования файлов ресурсов локализации, которые подробно описаны [здесь](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Отсутствующие ресурсы

В число распространенных причин, по которым не удается найти ресурсы, входят следующие:

- имена ресурсов в файле `resx` или запросе средства локализации написаны с ошибками;
- ресурс отсутствует в файле `resx` для одних языков, но существует для других;
- если проблема сохраняется, просмотрите подробные сведения об отсутствующих ресурсах в сообщениях журнала локализации (расположенных на уровне ведения журнала `Debug`).

_**Подсказка.** Если вы используете `CookieRequestCultureProvider`, убедитесь, что языки и региональные параметры в значениях файлов cookie локализации указаны без одинарных кавычек. Например, `c='en-UK'|uic='en-US'` — это недопустимое значение файла cookie, а `c=en-UK|uic=en-US` — допустимое._

## <a name="resources--class-libraries-issues"></a>Проблемы с файлами ресурсов и библиотеками классов

По умолчанию ASP.NET Core позволяет библиотекам классов находить соответствующие файлы ресурсов с помощью класса [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

В число распространенных проблем с библиотеками классов входят следующие:
- из-за отсутствия класса `ResourceLocationAttribute` в библиотеке классов `ResourceManagerStringLocalizerFactory` не сможет обнаруживать ресурсы;
- именование файлов ресурсов. Дополнительные сведения см. в разделе [Проблемы именования файлов ресурсов](#resource-file-naming-issues).
- Изменение корневого пространства имен библиотеки классов. Дополнительные сведения см. в разделе [Проблемы с корневым пространством имен](#root-namespace-issues).

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider не работает должным образом

У класса `RequestLocalizationOptions` есть три поставщика по умолчанию:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) позволяет настроить способ предоставления сведений о языке и региональных параметрах локализации в приложении. `CustomRequestCultureProvider` используется, если поставщики по умолчанию не соответствуют вашим требованиям.

- Чаще всего неправильная работа настраиваемого поставщика связана с тем, что он не является первым в списке `RequestCultureProviders`. Действия для устранения этой проблемы.

- Вставьте настраиваемый поставщик в позицию 0 в списке `RequestCultureProviders`, как показано ниже.

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- С помощью метода расширения `AddInitialRequestCultureProvider` задайте настраиваемый поставщик в качестве исходного.

## <a name="root-namespace-issues"></a>Проблемы с корневым пространством имен

Если корневое пространство имен сборки отличается от имени сборки, локализация не работает по умолчанию. Чтобы избежать этой проблемы, используйте свойство [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), которое подробно описано [здесь](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

> [!WARNING]
> Это может произойти, если имя проекта — недопустимый идентификатор .NET. Например, `my-project-name.csproj` будет использовать корневое пространство имен `my_project_name` и имя сборки `my-project-name`, что повлечет эту ошибку. 

## <a name="resources--build-action"></a>Ресурсы и действие при сборке

Если вы используете файлы ресурсов для локализации, важно, чтобы у них было соответствующее действие при сборке. Они должны быть **внедренными ресурсами**, в противном случае `ResourceStringLocalizer` не сможет их найти.
