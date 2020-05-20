<a name="codegenerator"></a> В следующей таблице представлены параметры генератора кода ASP.NET Core:

| Параметр               | Description|
| ----------------- | ------------ |
| -M  | Имя модели. |
| -dc  | Класс `DbContext` для использования. |
| -udl | Использование макета по умолчанию |
| -outDir | Относительный путь к папке выходных данных для создания представлений |
| --referenceScriptLibraries | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте коммутатор `h`.

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
