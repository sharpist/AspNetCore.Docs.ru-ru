<a name="csc"></a>

Рассмотрим следующий метод `ConfigureServices`, который регистрирует службы и настраивает параметры:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Связанные группы регистраций можно переместить в метод расширения для регистрации служб. Например, службы конфигурации добавляются в следующий класс:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Остальные службы регистрируются в аналогичном классе. Следующий метод `ConfigureServices` использует новые методы расширения для регистрации служб:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Примечание._** Каждый метод расширения `services.Add{GROUP_NAME}` добавляет и, возможно, настраивает службы. Например, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> добавляет контроллеры MVC служб с необходимыми представлениями, а <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> — службы, требуемые для работы Razor Pages. Рекомендуем придерживаться этого соглашения об именовании в приложениях. Поместите методы расширения в пространство имен <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, чтобы инкапсулировать группы зарегистрированных служб.
