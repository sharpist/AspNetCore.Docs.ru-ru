> [!WARNING]
> Соответствие параметра **catch-all** маршрутам может быть неправильным из-за [ошибки](https://github.com/dotnet/aspnetcore/issues/18677) в маршрутизации. Приложения, на работу которых влияет эта ошибка, обладают следующими характеристиками:
>
> * Маршрут catch-all, например `{**slug}"`.
> * Маршрут catch-all не соответствует необходимым запросам.
> * После удаления других маршрутов маршрут catch-all начинает функционировать должным образом.
>
> Ознакомьтесь с примерами [18677](https://github.com/dotnet/aspnetcore/issues/18677) и [16579](https://github.com/dotnet/aspnetcore/issues/16579), в которых встречается эта ошибка, на сайте GitHub.
>
> Исправление для этой ошибки содержится в [пакете SDK для .NET Core 3.1.301 и более поздних версиях](https://dotnet.microsoft.com/download/dotnet-core/3.1). Следующий код задает внутренний коммутатор, исправляет эту ошибку:
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```