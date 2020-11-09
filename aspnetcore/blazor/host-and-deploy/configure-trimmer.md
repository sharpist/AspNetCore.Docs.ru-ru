---
title: 'Настройка средства обрезки для ASP.NET Core Blazor'
author: guardrex
description: Узнайте, как управлять компоновщиком (средством обрезки) для промежуточного языка (IL) при сборке приложения Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055806"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="86398-103">Настройка средства обрезки для ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="86398-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="86398-104">Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="86398-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="86398-105">Blazor WebAssembly выполняет обрезку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) для уменьшения размера публикуемых выходных данных.</span><span class="sxs-lookup"><span data-stu-id="86398-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="86398-106">Обрезка приложения оптимизируется в зависимости от размера, но это может иметь негативные последствия.</span><span class="sxs-lookup"><span data-stu-id="86398-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="86398-107">Приложения, использующие отражение или связанные динамические функции, могут прерываться при обрезке, так как средство обрезки не знает об этом динамическом поведении и не может определить, какие типы необходимы для отражения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="86398-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="86398-108">Чтобы обрезать такие приложения, средство обрезки должно быть уведомлено о любых типах, необходимых для отражения в коде и в пакетах или платформах, от которых зависит приложение.</span><span class="sxs-lookup"><span data-stu-id="86398-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="86398-109">Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, важно часто тестировать публикуемые выходные данные во время разработки.</span><span class="sxs-lookup"><span data-stu-id="86398-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="86398-110">Чтобы отключить обрезку для приложений .NET, задайте для свойства `PublishTrimmed` MSBuild в файле проекта приложения значение `false`.</span><span class="sxs-lookup"><span data-stu-id="86398-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="86398-111">Дополнительные параметры для настройки средства обрезки приведены в статье [Параметры обрезки](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="86398-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86398-112">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="86398-112">Additional resources</span></span>

* [<span data-ttu-id="86398-113">Обрезка автономных развертываний и исполняемых файлов</span><span class="sxs-lookup"><span data-stu-id="86398-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
