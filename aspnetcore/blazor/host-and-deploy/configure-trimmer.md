---
title: Настройка средства обрезки для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как управлять компоновщиком (средством обрезки) для промежуточного языка (IL) при сборке приложения Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055806"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Настройка средства обрезки для ASP.NET Core Blazor

Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)

Blazor WebAssembly выполняет обрезку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) для уменьшения размера публикуемых выходных данных.

Обрезка приложения оптимизируется в зависимости от размера, но это может иметь негативные последствия. Приложения, использующие отражение или связанные динамические функции, могут прерываться при обрезке, так как средство обрезки не знает об этом динамическом поведении и не может определить, какие типы необходимы для отражения во время выполнения. Чтобы обрезать такие приложения, средство обрезки должно быть уведомлено о любых типах, необходимых для отражения в коде и в пакетах или платформах, от которых зависит приложение.

Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, важно часто тестировать публикуемые выходные данные во время разработки.

Чтобы отключить обрезку для приложений .NET, задайте для свойства `PublishTrimmed` MSBuild в файле проекта приложения значение `false`.

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
Дополнительные параметры для настройки средства обрезки приведены в статье [Параметры обрезки](/dotnet/core/deploying/trimming-options).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Обрезка автономных развертываний и исполняемых файлов](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
