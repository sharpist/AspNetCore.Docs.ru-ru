В следующей таблице представлены параметры генератора кода ASP.NET Core.

| Параметр               | Description|
| ----------------- | ------------ |
| -M  | Имя модели. |
| -dc  | Контекст данных |
| -udl | Использование макета по умолчанию |
| --relativeFolderPath | Относительный путь к папке выходных данных для создания файлов |
| --useDefaultLayout | Макет по умолчанию следует использовать для представлений. |
| --referenceScriptLibraries | Добавляет `_ValidationScriptsPartial` для страниц редактирования и создания |

Чтобы получить справку по команде `aspnet-codegenerator controller`, используйте коммутатор `h`.

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

См. подробнее о [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
