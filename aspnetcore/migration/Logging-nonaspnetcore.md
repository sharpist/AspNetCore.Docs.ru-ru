---
<span data-ttu-id="855a5-101">Title: миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0 author: пакрим Description: Узнайте, как перенести приложение non-ASP.NET Core, использующее Microsoft. Extensions. Logging от 2,1 до 2,2 или 3,0.</span><span class="sxs-lookup"><span data-stu-id="855a5-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="855a5-102">MS. author: пакрим MS. Custom: MVC MS. Дата: 01/04/2019 No-Loc:</span><span class="sxs-lookup"><span data-stu-id="855a5-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="855a5-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="855a5-103">'Blazor'</span></span>
- <span data-ttu-id="855a5-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="855a5-104">'Identity'</span></span>
- <span data-ttu-id="855a5-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="855a5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="855a5-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="855a5-106">'Razor'</span></span>
- <span data-ttu-id="855a5-107">' SignalR ' UID: миграция/ведение журнала-нонаспнеткоре</span><span class="sxs-lookup"><span data-stu-id="855a5-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="855a5-108">Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0</span><span class="sxs-lookup"><span data-stu-id="855a5-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="855a5-109">В этой статье описаны общие действия по переносу приложения non-ASP.NET Core, использующего `Microsoft.Extensions.Logging` от 2,1 до 2,2 или 3,0.</span><span class="sxs-lookup"><span data-stu-id="855a5-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="855a5-110">С версии 2.1 на 2.2</span><span class="sxs-lookup"><span data-stu-id="855a5-110">2.1 to 2.2</span></span>

<span data-ttu-id="855a5-111">Создайте `ServiceCollection` и вызовите вручную `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="855a5-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="855a5-112">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="855a5-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="855a5-113">2,2. Пример:</span><span class="sxs-lookup"><span data-stu-id="855a5-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="855a5-114">от 2,1 до 3,0</span><span class="sxs-lookup"><span data-stu-id="855a5-114">2.1 to 3.0</span></span>

<span data-ttu-id="855a5-115">В 3,0 используйте `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="855a5-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="855a5-116">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="855a5-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="855a5-117">3,0. Пример:</span><span class="sxs-lookup"><span data-stu-id="855a5-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="855a5-118">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="855a5-118">Additional resources</span></span>

* <span data-ttu-id="855a5-119">[Пакет NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="855a5-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
