## <a name="debug-diagnostics"></a><span data-ttu-id="f5c98-101">Отладка диагностики</span><span class="sxs-lookup"><span data-stu-id="f5c98-101">Debug diagnostics</span></span>

<span data-ttu-id="f5c98-102">Для подробного вывода диагностики построения маршрутов задайте для `Logging:LogLevel:Microsoft` значение `Debug`.</span><span class="sxs-lookup"><span data-stu-id="f5c98-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="f5c98-103">В среде разработки задайте уровень ведения журнала в *appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="f5c98-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
