Выполните следующие команды интерфейса командной строки .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

Приведенные выше команды добавляют следующие компоненты:

* [средство формирования шаблонов](xref:fundamentals/tools/dotnet-aspnet-codegenerator);
* средства Entity Framework Core для .NET Core CLI;
* поставщик EF Core для SQLite, который устанавливает пакет EF Core в качестве зависимости;
* Пакеты, необходимые для формирования шаблонов: `Microsoft.VisualStudio.Web.CodeGeneration.Design` и `Microsoft.EntityFrameworkCore.SqlServer`.

Рекомендации по настройке нескольких сред, позволяющей приложению настраивать свои контексты баз данных в среде, см. в разделе <xref:fundamentals/environments#environment-based-startup-class-and-methods>.

[!INCLUDE[](~/includes/scaffoldTFM.md)]