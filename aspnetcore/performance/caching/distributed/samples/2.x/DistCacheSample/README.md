# <a name="aspnet-core-distributed-cache-sample"></a>Пример распределенного кэша ASP.NET Core

В этом примере демонстрируется использование распределенного кэша. В этом примере демонстрируется сценарий, описанный в разделе [Работа с распределенным кэшем ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

В рабочей среде пример приложения настроен для использования распределенного кэша SQL Server. Чтобы перенастроить приложение для использования распределенного кэша Redis, измените директиву препроцессора в верхней части файла *Startup.CS* , чтобы использовать Redis ( `#define Redis // SQLServer` ). Дополнительные сведения см. [в разделе Директивы препроцессора в образце кода](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
