## <a name="debug-diagnostics"></a>Отладка диагностики

Для подробного вывода диагностики построения маршрутов задайте для `Logging:LogLevel:Microsoft` значение `Debug`. В среде разработки задайте уровень ведения журнала в *appsettings.Development.js*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
