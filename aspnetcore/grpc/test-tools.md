---
title: Службы тестирования со средствами gRPC
author: jamesnk
description: Узнайте, как тестировать службы с помощью средств gRPC. gRPCurl — программа командной строки для взаимодействия со службами gRPC. gRPCui — интерактивный пользовательский веб-интерфейс.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594376"
---
# <a name="test-services-with-grpc-tools"></a>Службы тестирования со средствами gRPC

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

Для gRPC доступен инструментарий, позволяющий разработчикам тестировать службы без сборки клиентских приложений. [gRPCurl](https://github.com/fullstorydev/grpcurl) — это программа командной строки, которая обеспечивает взаимодействие со службами gRPC. [gRPCui](https://github.com/fullstorydev/grpcui) предоставляет интерактивный пользовательский веб-интерфейс для gRPC.

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

## <a name="setup-grpc-reflection"></a>Настройка отражения gRPC

`grpcurl` должен быть известен контракт Protobuf служб, чтобы их можно было вызывать. Это можно сделать двумя способами.

* Используйте отражение gRPC для обнаружения контрактов служб.
* Укажите файлы *PROTO* в аргументах командной строки.

gRPCurl проще использовать с отражением gRPC и обнаружением служб. В gRPC ASP.NET Core есть встроенная поддержка отражения gRPC, обеспечиваемая пакетом [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection). Чтобы настроить отражение в приложении, выполните указанные ниже действия.

* Добавьте ссылку на пакет `Grpc.AspNetCore.Server.Reflection`.
* Зарегистрируйте отражение в файле *Startup.cs*:
  * `AddGrpcReflection` для регистрации служб, обеспечивающих отражение;
  * `MapGrpcReflectionService` для добавления конечной точки службы отражения.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>Используйте `grpcurl`.

Аргумент `-help` описывает параметры командной строки `grpcurl`.

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Обнаружение служб

Используйте команду `describe` для просмотра служб, определенных сервером:

```powershell
> grpcurl.exe localhost:5001 describe
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
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Вызов служб gRPC

Вызовите службу gRPC, указав имена службы и метода вместе с аргументом JSON, который представляет сообщение запроса. Сообщение JSON преобразуется в Protobuf и отправляется службе.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Предшествующий пример:

* Аргумент `-d` задает сообщение запроса с помощью JSON. Этот аргумент должен предшествовать адресу сервера и имени метода.
* Вызывает метод `SayHello` для службы `greeter.Greeter`.
* Выводит ответное сообщение в формате JSON.

## <a name="about-grpcui"></a>Сведения о gRPCui

gRPCui — это интерактивный пользовательский веб-интерфейс для gRPC. Он основан на gRPCurl и обеспечивает графический пользовательский интерфейс для обнаружения и тестирования служб gRPC, аналогичный таким средствам HTTP, как Postman.

Сведения о скачивании и установке `grpcui` см. на [домашней странице gRPCui в GitHub](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Использование `grpcui`

Выполните команду `grpcui`, указав адрес нужного сервера в качестве аргумента.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Средство откроет окно браузера с интерактивным пользовательским веб-интерфейсом. Службы gRPC обнаруживаются автоматически с помощью отражения gRPC.

![Пользовательский веб-интерфейс gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Домашняя страница gRPCurl в GitHub](https://github.com/fullstorydev/grpcurl)
* [Домашняя страница gRPCui в GitHub](https://github.com/fullstorydev/grpcui)
* [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
