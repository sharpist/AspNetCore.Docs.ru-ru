---
title: Создание веб-интерфейсов API JSON из gRPC
author: jamesnk
description: Узнайте, как создавать интерфейсы API JSON HTTP для служб gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/httpapi
ms.openlocfilehash: 45e2a1a5e6a9f00294147db769454b78c5b866e5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059940"
---
# <a name="create-json-web-apis-from-grpc"></a>Создание веб-интерфейсов API JSON из gRPC

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)

> [!IMPORTANT]
> API HTTP gRPC — это экспериментальный проект и пока не является готовым продуктом. Наши задачи:
>
> * Проверка, работает ли наш метод создания веб-API JSON для служб gRPC.
> * Получение отзывов, полезен ли этот метод для разработчиков .NET.
>
> Просим вас [оставлять отзывы](https://github.com/grpc/grpc-dotnet/issues/167), чтобы мы могли понять, создаем ли мы удобный и эффективный продукт для разработчиков.

gRPC — это современный способ обмена данными между приложениями. gRPC использует HTTP/2, потоковую передачу, Protobuf и контракты сообщений для создания высокопроизводительных служб в реальном времени.

gRPC имеет единственное ограничение — его можно использовать не на каждой платформе. Браузеры не полностью поддерживают HTTP/2, поэтому REST и JSON являются основными способами передачи данных в браузерные приложения. Даже при всех преимуществах gRPC REST и JSON играют важную роль в современных приложениях. Создание веб-API gRPC * **и** _ JSON добавляет нежелательные затраты на разработку приложений.

В этом документе рассматривается создание веб-API JSON с помощью служб gRPC.

## <a name="grpc-http-api"></a>API gRPC HTTP

API gRPC HTTP — это экспериментальная сборка для ASP.NET Core, которая создает API-интерфейсы RESTful JSON для служб gRPC. После настройки API gRPC HTTP позволяет приложениям вызывать службы gRPC, используя следующие привычные понятия HTTP:

_ HTTP-команды
* привязку параметра URL-адреса;
* запросы и ответы JSON.

gRPC по-прежнему можно использовать для вызова служб.

### <a name="usage"></a>Использование

1. Добавьте ссылку на пакет в [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).
1. Зарегистрируйте службы в *Startup.cs* с помощью `AddGrpcHttpApi`.
1. Добавьте в свой проект файлы [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) и [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto).
1. Добавьте заметки в методы gRPC в ваших файлах *PROTO* с маршрутами и привязками HTTP:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Теперь метод `SayHello` gRPC можно вызывать как gRPC + Protobuf и как API HTTP:

* Запрос: `HTTP/1.1 GET /v1/greeter/world`
* Ответ: `{ "message": "Hello world" }`

Серверные журналы показывают, что HTTP-вызов выполняется службой gRPC. API gRPC HTTP сопоставляет входящий HTTP-запрос с сообщением gRPC, а затем преобразует ответное сообщение в JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Ниже приводится простой пример. Дополнительные возможности настройки см. в разделе [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).

### <a name="grpc-http-api-vs-grpc-web"></a>Сравнение API gRPC HTTP и gRPC-Web

И API gRPC HTTP, и gRPC-Web позволяют вызывать службы gRPC из браузера. Однако способы, которыми они это делают, отличаются.

* gRPC-Web позволяет браузерным приложениям вызывать службы gRPC из браузера с помощью клиента gRPC-Web и Protobuf. gRPC-Web требует, чтобы браузерное приложение создавало клиент gRPC, а преимущество заключается в том, что отправляются небольшие и быстрые сообщения Protobuf.
* API gRPC HTTP позволяет браузерным приложениям вызывать службы gRPC с помощью JSON, как если бы они были API RESTful. Браузерному приложению не нужно создавать клиент gRPC и не нужно ничего знать о gRPC.

Для API gRPC HTTP не создается сформированный клиент. Предыдущую службу `Greeter` можно вызывать с помощью API JavaScript браузера:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Экспериментальное состояние

API gRPC HTTP является экспериментом. Это решение не завершено и не поддерживается. Мы заинтересованы в этой технологии, и она предоставляет разработчикам приложений возможность быстро и одновременно создавать службы gRPC и JSON. Мы не принимаем на себя обязательства по завершению этого проекта API gRPC HTTP.

Мы хотим оценить заинтересованность разработчиков в API gRPC HTTP. Если вас заинтересовала технология API gRPC HTTP, просим вас [отправить отзыв](https://github.com/grpc/grpc-dotnet/issues/167).

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) — еще одна технология создания API RESTful JSON из служб gRPC. Она использует такие же заметки в файлах *PROTO* , чтобы сопоставлять понятия HTTP со службами gRPC.

Главное различие между grpc-gateway и API gRPC HTTP состоит в том, что grpc-gateway использует формирование кода для создания обратного прокси-сервера. Обратный прокси-сервер преобразует вызовы RESTful в gRPC, а затем отправляет их в службу gRPC.

Сведения об установке и использовании grpc-gateway см. в [документации по grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация по google.api.HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
