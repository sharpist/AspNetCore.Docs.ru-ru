> [!WARNING]
> <span data-ttu-id="5e940-101">Соответствие параметра **catch-all** маршрутам может быть неправильным из-за [ошибки](https://github.com/dotnet/aspnetcore/issues/18677) в маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="5e940-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="5e940-102">Приложения, на работу которых влияет эта ошибка, обладают следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="5e940-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="5e940-103">Маршрут catch-all, например `{**slug}"`.</span><span class="sxs-lookup"><span data-stu-id="5e940-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="5e940-104">Маршрут catch-all не соответствует необходимым запросам.</span><span class="sxs-lookup"><span data-stu-id="5e940-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="5e940-105">После удаления других маршрутов маршрут catch-all начинает функционировать должным образом.</span><span class="sxs-lookup"><span data-stu-id="5e940-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="5e940-106">Ознакомьтесь с примерами [18677](https://github.com/dotnet/aspnetcore/issues/18677) и [16579](https://github.com/dotnet/aspnetcore/issues/16579), в которых встречается эта ошибка, на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="5e940-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="5e940-107">Для этой ошибки запланировано исправление.</span><span class="sxs-lookup"><span data-stu-id="5e940-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="5e940-108">После выпуска исправления данная документация будет обновлена.</span><span class="sxs-lookup"><span data-stu-id="5e940-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="5e940-109">После выпуска исправления с помощью приведенного ниже кода можно будет установить внутренний переключатель, который исправит эту ошибку:</span><span class="sxs-lookup"><span data-stu-id="5e940-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```