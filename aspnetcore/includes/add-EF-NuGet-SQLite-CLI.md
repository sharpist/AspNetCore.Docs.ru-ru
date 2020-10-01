<span data-ttu-id="4262c-101">Выполните следующие команды интерфейса командной строки .NET Core:</span><span class="sxs-lookup"><span data-stu-id="4262c-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="4262c-102">Приведенные выше команды добавляют следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="4262c-102">The preceding commands add:</span></span>

* <span data-ttu-id="4262c-103">[средство формирования шаблонов](xref:fundamentals/tools/dotnet-aspnet-codegenerator);</span><span class="sxs-lookup"><span data-stu-id="4262c-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="4262c-104">средства Entity Framework Core для .NET Core CLI;</span><span class="sxs-lookup"><span data-stu-id="4262c-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="4262c-105">поставщик EF Core для SQLite, который устанавливает пакет EF Core в качестве зависимости;</span><span class="sxs-lookup"><span data-stu-id="4262c-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="4262c-106">Пакеты, необходимые для формирования шаблонов: `Microsoft.VisualStudio.Web.CodeGeneration.Design` и `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="4262c-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="4262c-107">Рекомендации по настройке нескольких сред, позволяющей приложению настраивать свои контексты баз данных в среде, см. в разделе <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="4262c-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]