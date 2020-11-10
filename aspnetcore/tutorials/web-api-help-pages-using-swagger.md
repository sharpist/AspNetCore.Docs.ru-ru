---
title: Документация по веб-API ASP.NET Core с использованием Swagger (OpenAPI)
author: RicoSuter
description: В этом учебнике приводится пошаговое руководство по добавлению Swagger для составления документации и страниц справки к приложению веб-API.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062458"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Документация по веб-API ASP.NET Core с использованием Swagger (OpenAPI)

Авторы: [Кристоф Ниенабер (Christoph Nienaber)](https://twitter.com/zuckerthoben) и [Рико Сутер (Rico Suter)](https://blog.rsuter.com/)

Swagger (OpenAPI) — это не зависящая от языка спецификация для описания REST API. Она позволяет компьютерам и пользователям лучше понять возможности REST API без прямого доступа к исходному коду. Ее основные цели:

* свести к минимуму объем работ, необходимых для соединения отдельных служб;
* сократить время, необходимое для точного документирования службы.

Две основные реализации OpenAPI для .NET — это [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) и [NSwag](https://github.com/RicoSuter/NSwag). См. следующие статьи:

* [Начало работы с Swashbuckle и ASP.NET Core](xref:tutorials/get-started-with-swashbuckle)
* [Начало работы с NSwag и ASP.NET Core](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenAPI и Swagger

Проект Swagger был передан OpenAPI Initiative в 2015 году и с тех пор называется OpenAPI. Оба имени взаимозаменяемы. Но "OpenAPI" относится к спецификации. "Swagger" относится к семейству с открытым исходным кодом и коммерческим продуктам от SmartBear, которые работают со спецификацией OpenAPI. Последующие продукты с открытым кодом, такие как [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), также относятся к семейству Swagger несмотря на то, что они не выпускаются SmartBear.

Иными словами:

* OpenAPI — это спецификация.
* Swagger — это инструментарий, использующий спецификацию OpenAPI. Например, OpenAPIGenerator и SwaggerUI.

## <a name="openapi-specification-openapijson"></a>Спецификация OpenAPI (openapi.json)

Спецификация OpenAPI — это документ, описывающий возможности API. Документ основан на XML и заметках атрибутов в контроллерах и моделях. Это основная часть потока OpenAPI, которая используется для управления такими инструментами, как SwaggerUI. По умолчанию он называется *openapi.json*. Ниже приведен сокращенный пример спецификации OpenAPI:

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Пользовательский интерфейс Swagger

[Пользовательский интерфейс Swagger](https://swagger.io/swagger-ui/) обеспечивает пользовательский веб-интерфейс, предоставляющий сведения о службе с использованием созданной спецификации OpenAPI. Swashbuckle и NSwag включают встроенную версию пользовательского интерфейса Swagger, чтобы его можно было разместить в приложении ASP.NET Core, используя вызов регистрации ПО промежуточного слоя. Пользовательский веб-интерфейс выглядит следующим образом:

![Пользовательский интерфейс Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Каждый метод открытого действия в ваших контроллерах можно протестировать в пользовательском интерфейсе. Выберите имя метода, чтобы развернуть соответствующий раздел. Добавьте все необходимые параметры и выберите **Попробуйте!** .

![Пример тестирования в Swagger метода GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> На снимках экрана используется пользовательский интерфейс Swagger версии 2. Пример версии 3 см. в разделе [Пример Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Следующие шаги

* [Начало работы с Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Начало работы с NSwag](xref:tutorials/get-started-with-nswag)
