---
title: Размещение и развертывание серверного приложения Blazor с помощью ASP.NET Core
author: guardrex
description: Сведения о размещении и развертывании серверного приложения Blazor с помощью ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 8c06d3a4d0d75a3e2fd9f699af38a23833fa8bce
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419948"
---
# <a name="host-and-deploy-blazor-server"></a>Размещение и развертывание серверного приложения Blazor

Авторы: [Люк Лэтем](https://github.com/guardrex), [Рэйнер Стропек](https://www.timecockpit.com) и [Дэниэл Рот](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Значения конфигурации узла

[Серверные приложения Blazor](xref:blazor/hosting-models#blazor-server) могут принимать [значения конфигурации универсального узла](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Развертывание

Если используется [модель размещения на сервере Blazor](xref:blazor/hosting-models#blazor-server), Blazor выполняется на сервере из приложения ASP.NET Core. Обновление элементов пользовательского интерфейса, обработка событий и вызовы JavaScript обрабатываются через подключение [SignalR](xref:signalr/introduction).

Необходим веб-сервер, позволяющий размещать приложения ASP.NET Core. Visual Studio содержит шаблон проекта **Серверное приложение Blazor** (шаблон `blazorserverside` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)).

## <a name="scalability"></a>Масштабируемость

Планируйте развертывание так, чтобы доступная инфраструктура максимально эффективно использовалась серверным приложением Blazor. Вопросы, связанные с масштабируемостью серверных приложений Blazor, рассматриваются в следующих статьях:

* [Статья со сведениями о серверных приложениях Blazor](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Сервер развертывания

В контексте масштабируемости отдельного сервера (вертикальное масштабирование) доступная приложению память с большой вероятностью будет первым ресурсом, расходуемым приложением при изменении потребностей пользователей. Объем доступной памяти на сервере влияет на:

* число активных каналов, которые может поддерживать сервер;
* задержку пользовательского интерфейса в клиенте.

Рекомендации по созданию безопасных и масштабируемых серверных приложений Blazor см. здесь: <xref:security/blazor/server/threat-mitigation>.

Каждый канал использует около 250 КБ памяти для минималистичного приложения в стиле *Hello World*. Размер канала зависит от кода приложения и требований к обслуживанию состояний для каждого компонента. Мы рекомендуем определять требования к ресурсам во время разработки приложения и инфраструктуры, но при планировании цели развертывания можно использовать следующие базовые показатели: если вы предполагаете, что приложение будет одновременно поддерживать 5000 пользователей, выделите для приложения по меньшей мере 1,3 ГБ серверной памяти (или около 273 КБ на пользователя).

### <a name="signalr-configuration"></a>Конфигурация SignalR

Серверные приложения Blazor используют службу SignalR в ASP.NET Core для обмена данными с браузером. [Условия размещения и масштабирования SignalR](xref:signalr/publish-to-azure-web-app) применяются и к серверным приложениям Blazor.

Blazor лучше всего работает с WebSocket в качестве транспортного механизма SignalR благодаря низкой задержке, надежности и [безопасности](xref:signalr/security). Продолжительный опрос используется службой SignalR, если подключения WebSocket недоступны или если приложение явно настроено на применение продолжительного опроса. При развертывании Службы приложений Azure настройте приложение на использование WebSocket в параметрах службы на портале Azure. Подробные сведения о настройке приложения для Службы приложений Azure см. в статье с [рекомендациями по публикации приложения SignalR](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Служба Azure SignalR

Мы рекомендуем использовать для серверных приложений Blazor[службу Azure SignalR](/azure/azure-signalr). Она позволяет вертикально масштабировать серверные приложения Blazor для одновременного использования большого числа подключений SignalR. Кроме того, глобальный охват службы SignalR и ее высокопроизводительные центры обработки данных обеспечивают низкую задержку благодаря доступности в разных регионах. Чтобы настроить приложение (и при необходимости подготовить к работе) для службы Azure SignalR, сделайте следующее:

1. Включите в службе поддержку *прикрепленных сеансов*, когда клиенты [перенаправляются обратно на тот же сервер при предварительной отрисовке](xref:blazor/hosting-models#connection-to-the-server). Установите параметр или значение конфигурации `ServerStickyMode` равным `Required`. Как правило, приложение создает конфигурацию, используя **один** из следующих подходов:

   * `Startup.ConfigureServices`.
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Конфигурация (воспользуйтесь **одним** из перечисленных ниже подходов):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * раздел **Конфигурация** > **Параметры приложения** для службы приложений на портале Azure (**Имя**: `Azure:SignalR:ServerStickyMode`, **Значение**: `Required`).

1. Создайте профиль публикации приложений Azure Apps в Visual Studio для серверного приложения Blazor.
1. Добавьте в профиль зависимость **службы Azure SignalR** . Если подписка Azure не имеет уже существующего экземпляра службы Azure SignalR для назначения приложению, выберите **Создайте новый экземпляр службы Azure SignalR** для предоставления нового экземпляра службы.
1. Опубликуйте приложение в Azure.

#### <a name="iis"></a>IIS

При использовании IIS включите:

* [WebSockets в IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Прикрепленные сеансы с маршрутизацией запросов приложений](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Создайте определение входящего трафика со следующими [заметками Kubernetes для прикрепленных сеансов](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux с Nginx

Для правильной работы WebSockets SignalR убедитесь, что для заголовков `Upgrade` и `Connection` прокси-сервера заданы следующие значения и `$connection_upgrade` сопоставлен с одним из следующих элементов:

* Значение заголовка обновления по умолчанию.
* `close`, если заголовок обновления отсутствует или пуст.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Дополнительные сведения см. в следующих статьях:

* [NGINX как прокси-сервер WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Использование прокси-сервера для WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux c Apache

Чтобы разместить приложение Blazor на основе Apache в Linux, настройте `ProxyPass` для трафика HTTP и WebSockets.

В следующем примере:

* Сервер Kestrel запущен на ведущем компьютере.
* Приложение прослушивает трафик на порту 5000.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Включите следующие модули:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Проверьте консоль браузера на наличие ошибок WebSockets. Примеры ошибок приведены далее.

* Firefox не может установить подключение к серверу по адресу ws://the-domain-name.tld/_blazor?id=XXX.
* Ошибка: Не удалось запустить транспорт "WebSockets": Ошибка: произошла ошибка транспорта.
* Ошибка: не удалось запустить транспорт "LongPolling". TypeError: этот транспорт не определен.
* Ошибка: не удается подключиться к серверу с помощью любого из доступных транспортов. Сбой WebSockets.
* Ошибка: невозможно отправить данные, если подключение не находится в состоянии "Подключено".

Дополнительные сведения см. в [документации по Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Измерение задержки сети

Для оценки задержки сети можно использовать [средства взаимодействия JavaScript](xref:blazor/call-javascript-from-dotnet), как показано в следующем примере:

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Для удобной работы с пользовательским интерфейсом мы рекомендуем обеспечить стабильную задержку не более 250 мс.
