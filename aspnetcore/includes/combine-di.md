<a name="csc"></a>

## <a name="combining-service-collection"></a>Объединение коллекций служб

Рассмотрим следующий объект `ConfigureServices`, содержащий несколько коллекций служб:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Связанные группы регистраций можно переместить в метод расширения для регистрации служб. Например, службы конфигурации добавляются в следующий класс:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

Остальные службы регистрируются в аналогичном классе. Следующий объект `ConfigureServices` использует новые методы расширения для регистрации служб:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***Примечание.*** Каждый метод расширения `services.Add{SERVICE_NAME}` добавляет и, возможно, настраивает службы. Например, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> добавляет контроллеры MVC служб с необходимыми представлениями. <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> добавляет необходимые службы Razor Pages. Рекомендуем придерживаться этого соглашения об именовании в приложениях. Поместите методы расширения в пространство имен [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), чтобы инкапсулировать группы зарегистрированных служб.