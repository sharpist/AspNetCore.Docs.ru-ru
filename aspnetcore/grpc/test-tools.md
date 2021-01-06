---
title: Тестирование служб gRPC с помощью gRPCurl в ASP.NET Core
author: jamesnk
description: Узнайте, как тестировать службы с помощью средств gRPC. gRPCurl — программа командной строки для взаимодействия со службами gRPC. gRPCui — интерактивный пользовательский веб-интерфейс.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058796"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Тестирование служб gRPC с помощью gRPCurl в ASP.NET Core

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

Для gRPC доступен инструментарий, позволяющий разработчикам тестировать службы без сборки клиентских приложений.

* [gRPCurl](https://github.com/fullstorydev/grpcurl) — это программа командной строки, которая обеспечивает взаимодействие со службами gRPC.
* [gRPCui](https://github.com/fullstorydev/grpcui) создается на основе gRPCurl и имеет интерактивный веб-интерфейс для gRPC, аналогичный таким средствам, как интерфейс Postman и Swagger.

В этой статье рассматриваются следующие задачи:

* скачивание и установка gRPCurl и gRPCui;
* настройка отражения gRPC с помощью приложения gRPC ASP.NET Core;
* обнаружение и тестирование служб gRPC с помощью `grpcurl`;
* взаимодействие со службами gRPC в браузере с помощью `grpcui`.

## <a name="about-grpcurl"></a>Сведения о gRPCurl

gRPCurl — это программа командной строки, разрабатываемая сообществом gRPC. Она обладает следующими возможностями:

* вызов служб gRPC, включая службы потоковой передачи;
* обнаружение служб с помощью [отражения gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md);
* формирование списка и описание служб gRPC;
* работа с безопасными (TLS) и небезопасными (обычными текстовыми) серверами.

Сведения о скачивании и установке `grpcurl` см. на [домашней странице gRPCurl в GitHub](https://github.com/fullstorydev/grpcurl#installation).

![Командная строка gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Настройка отражения gRPC

`grpcurl` должен быть известен контракт Protobuf служб, чтобы их можно было вызывать. Это можно сделать двумя способами.

* Настройка [отражения gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) на сервере. gRPCurl автоматически обнаруживает контракты служб.
* Указание файлов `.proto` в аргументах командной строки.

gRPCurl проще использовать с отражением gRPC. Отражение gRPC добавляет в приложение новую службу gRPC, которую клиенты могут вызывать для обнаружения служб.

В gRPC ASP.NET Core есть встроенная поддержка отражения gRPC, обеспечиваемая пакетом [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection). Чтобы настроить отражение в приложении, выполните указанные ниже действия.

* Добавьте ссылку на пакет `Grpc.AspNetCore.Server.Reflection`.
* Зарегистрируйте отражение в файле `Startup.cs`:
  * `AddGrpcReflection` для регистрации служб, обеспечивающих отражение;
  * `MapGrpcReflectionService` для добавления конечной точки службы отражения.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

После настройки отражения gRPC:

* Служба отражения gRPC добавляется в серверное приложение.
* Клиентские приложения, поддерживающие отражение gRPC, могут вызывать службу отражения для обнаружения служб, размещенных на сервере.
* Службы gRPC по-прежнему вызываются из клиента. Отражение обеспечивает только обнаружение службы и не выполняет обход безопасности на стороне сервера. Для успешного вызова конечных точек, защищенных с помощью [проверки подлинности и авторизации](xref:grpc/authn-and-authz), требуется, чтобы вызывающий объект передавал конечной точке учетные данные.

## <a name="use-grpcurl"></a>Используйте `grpcurl`.

Аргумент `-help` описывает параметры командной строки `grpcurl`.

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Обнаружение служб

Используйте команду `describe` для просмотра служб, определенных сервером:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Предшествующий пример:

* Выполняет команду `describe` на сервере `localhost:5001`.
* Выводит службы и методы, возвращаемые отражением gRPC.
  * `Greeter` — это служба, реализуемая приложением.
  * `ServerReflection` — это служба, добавляемая пакетом `Grpc.AspNetCore.Server.Reflection`.

Используйте `describe` в сочетании с именем службы, метода или сообщения, чтобы просмотреть сведения о них:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Вызов служб gRPC

Вызовите службу gRPC, указав имена службы и метода вместе с аргументом JSON, который представляет сообщение запроса. Сообщение JSON преобразуется в Protobuf и отправляется службе.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

В предыдущем примере:

* Аргумент `-d` задает сообщение запроса с помощью JSON. Этот аргумент должен предшествовать адресу сервера и имени метода.
* Вызывает метод `SayHello` для службы `greeter.Greeter`.
* Выводит ответное сообщение в формате JSON.

## <a name="about-grpcui"></a>Сведения о gRPCui

gRPCui — это интерактивный пользовательский веб-интерфейс для gRPC. Он основан на gRPCurl и обеспечивает графический пользовательский интерфейс для обнаружения и тестирования служб gRPC, аналогичный таким средствам HTTP, как интерфейс Postman или Swagger.

Сведения о скачивании и установке `grpcui` см. на [домашней странице gRPCui в GitHub](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Использование `grpcui`

Выполните команду `grpcui`, указав адрес нужного сервера в качестве аргумента:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Средство откроет окно браузера с интерактивным пользовательским веб-интерфейсом. Службы gRPC обнаруживаются автоматически с помощью отражения gRPC.

![Пользовательский веб-интерфейс gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Домашняя страница gRPCurl в GitHub](https://github.com/fullstorydev/grpcurl)
* [Домашняя страница gRPCui в GitHub](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
