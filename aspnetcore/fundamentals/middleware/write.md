---
название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="write-custom-aspnet-core-middleware"></a>Написание пользовательского ПО промежуточного слоя ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)

ПО промежуточного слоя — это программное обеспечение, выстраиваемое в виде конвейера приложения для обработки запросов и откликов. ASP.NET Core предоставляет широкий набор встроенных компонентов ПО промежуточного слоя, но в некоторых случаях может потребоваться написать пользовательское ПО промежуточного слоя.

> [!NOTE]
> В этом разделе показано, как разработать ПО промежуточного слоя *convention-based*. Подход, использующий строгую типизацию и активацию по запросу, описан здесь: <xref:fundamentals/middleware/extensibility>.

## <a name="middleware-class"></a>Класс ПО промежуточного слоя

ПО промежуточного слоя обычно инкапсулируется в класс и предоставляется с помощью метода расширения. Рассмотрим следующее ПО промежуточного слоя, которое задает язык и региональные параметры для текущего запроса из строки запроса.

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Приведенный выше пример кода демонстрирует создание компонента промежуточного слоя. Дополнительные сведения о встроенной поддержке локализации в ASP.NET Core см. в разделе <xref:fundamentals/localization>.

Протестируйте ПО промежуточного слоя, передав язык и региональные параметры. Например, выполните запрос `https://localhost:5001/?culture=no`.

Следующий код перемещает делегат ПО промежуточного слоя в класс.

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Класс ПО промежуточного слоя должен включать следующее:

* Открытый конструктор с параметром типа <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Открытый метод с именем `Invoke` или `InvokeAsync`. Этот метод должен:
  * вернуть `Task`;
  * принять первый параметр типа <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
Дополнительные параметры для конструктора и `Invoke`/`InvokeAsync` заполняются с помощью [внедрения зависимости](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Зависимости ПО промежуточного слоя

ПО промежуточного слоя должно соответствовать [принципу явных зависимостей](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies), предоставляя свои зависимости в своем конструкторе. ПО промежуточного слоя создается один раз за *время существования приложения*. В разделе [Зависимости отдельных запросов](#per-request-middleware-dependencies) приведены сведения о том, как использовать службы совместно с ПО промежуточного слоя внутри запроса.

Компоненты промежуточного слоя могут разрешать свои зависимости, возникшие в результате [внедрения зависимостей](xref:fundamentals/dependency-injection), за счет параметров конструктора. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) также может принимать дополнительные параметры напрямую.

## <a name="per-request-middleware-dependencies"></a>Зависимости ПО промежуточного слоя для отдельных запросов

Так как ПО промежуточного слоя создается при запуске приложения, а не для отдельных запросов, службы времени существования *scoped*, используемые конструкторами ПО промежуточного слоя, не являются общими с другими типами, возникшими в результате внедрения зависимостей, в каждом из запросов. Если необходимо предоставить службу *scoped* для совместного использования ПО промежуточного слоя и другими типами, добавьте ее в сигнатуру метода `Invoke`. Метод `Invoke` может принимать дополнительные параметры, заполняемые при внедрении зависимостей.

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a>Метод расширения ПО промежуточного слоя

Следующий метод расширения предоставляет ПО промежуточного слоя посредством <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Следующий код вызывает ПО промежуточного слоя из `Startup.Configure`.

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
