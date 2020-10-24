::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="f5a6c-101">Соответствие параметра **catch-all** маршрутам может быть неправильным из-за [ошибки](https://github.com/dotnet/aspnetcore/issues/18677) в маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="f5a6c-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="f5a6c-102">Приложения, на работу которых влияет эта ошибка, обладают следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="f5a6c-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="f5a6c-103">Маршрут catch-all, например `{**slug}"`.</span><span class="sxs-lookup"><span data-stu-id="f5a6c-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="f5a6c-104">Маршрут catch-all не соответствует необходимым запросам.</span><span class="sxs-lookup"><span data-stu-id="f5a6c-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="f5a6c-105">После удаления других маршрутов маршрут catch-all начинает функционировать должным образом.</span><span class="sxs-lookup"><span data-stu-id="f5a6c-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="f5a6c-106">Ознакомьтесь с примерами [18677](https://github.com/dotnet/aspnetcore/issues/18677) и [16579](https://github.com/dotnet/aspnetcore/issues/16579), в которых встречается эта ошибка, на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="f5a6c-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="f5a6c-107">Опциональное исправление для этой ошибки содержится в [пакете SDK для .NET Core начиная с версии 3.1.301](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f5a6c-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="f5a6c-108">Следующий код задает внутренний переключатель, исправляющий эту ошибку:</span><span class="sxs-lookup"><span data-stu-id="f5a6c-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end