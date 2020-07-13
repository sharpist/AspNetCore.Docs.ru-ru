---
title: Страницы справки по веб-API ASP.NET Core с использованием Swagger (OpenAPI)
author: RicoSuter
description: В этом учебнике приводится пошаговое руководство по добавлению Swagger для составления документации и страниц справки к приложению веб-API.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 66b8278e84df5ee56582254ebe2dc99ada98a9dc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060310"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="ee2cf-103">Страницы справки по веб-API ASP.NET Core с использованием Swagger (OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="ee2cf-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="ee2cf-104">Авторы: [Кристоф Ниенабер (Christoph Nienaber)](https://twitter.com/zuckerthoben) и [Рико Сутер (Rico Suter)](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="ee2cf-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="ee2cf-105">При использовании веб-API разработчику бывает сложно разобраться в различных методах.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="ee2cf-106">[Swagger](https://swagger.io/) (также называется [OpenAPI](https://www.openapis.org/)) позволяет решить проблему создания полезной документации и страниц справки для веб-интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="ee2cf-107">Он имеет такие преимущества, как интерактивная документация, создание пакета SDK для клиента и возможность обнаружения API.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="ee2cf-108">В этой статье демонстрируется реализация [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) и [NSwag](https://github.com/RicoSuter/NSwag) в .NET Swagger:</span><span class="sxs-lookup"><span data-stu-id="ee2cf-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="ee2cf-109">**Swashbuckle.AspNetCore** — это проект с открытым исходным кодом, предназначенный для создания документов Swagger по веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="ee2cf-110">**NSwag** — это еще один проект с открытым кодом для создания документации Swagger и интеграции [пользовательского интерфейса Swagger](https://swagger.io/swagger-ui/) или [ReDoc](https://github.com/Rebilly/ReDoc) в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="ee2cf-111">NSwag также предлагает подходы для создания C# и клиентского кода TypeScript для API.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="ee2cf-112">Что такое Swagger (OpenAPI)?</span><span class="sxs-lookup"><span data-stu-id="ee2cf-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="ee2cf-113">Swagger — это не зависящая от языка спецификация для описания [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="ee2cf-114">Проект Swagger был передан [OpenAPI Initiative](https://www.openapis.org/), где он теперь называется OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="ee2cf-115">Оба названия равнозначны, но рекомендуется использовать OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="ee2cf-116">Он позволяет компьютерам и пользователям лучше понять возможности службы без прямого доступа к реализации (исходный код, доступ к сети, документация).</span><span class="sxs-lookup"><span data-stu-id="ee2cf-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="ee2cf-117">Одна из его задач — свести к минимуму объем работ, необходимых для соединения отдельных служб.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="ee2cf-118">Кроме того, он позволяет сократить время, необходимое для точного документирования службы.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="ee2cf-119">Спецификация OpenAPI (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="ee2cf-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="ee2cf-120">В основе потока OpenAPI лежит спецификация: по умолчанию это документ с именем *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="ee2cf-121">Она создается цепочкой инструментов OpenAPI (или их сторонней реализацией) на основе вашей службы.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="ee2cf-122">Он описывает возможности API и способы доступа к нему через HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="ee2cf-123">Он управляет пользовательским интерфейсом Swagger и используется цепочкой инструментов, чтобы включить обнаружение и создание клиентского кода.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="ee2cf-124">Ниже приведен сокращенный пример спецификации OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="ee2cf-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="ee2cf-125">Пользовательский интерфейс Swagger</span><span class="sxs-lookup"><span data-stu-id="ee2cf-125">Swagger UI</span></span>

<span data-ttu-id="ee2cf-126">[Пользовательский интерфейс Swagger](https://swagger.io/swagger-ui/) обеспечивает пользовательский веб-интерфейс, предоставляющий сведения о службе с использованием созданной спецификации OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="ee2cf-127">Swashbuckle и NSwag включают встроенную версию пользовательского интерфейса Swagger, чтобы его можно было разместить в приложении ASP.NET Core, используя вызов регистрации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="ee2cf-128">Пользовательский веб-интерфейс выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ee2cf-128">The web UI looks like this:</span></span>

![Пользовательский интерфейс Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="ee2cf-130">Каждый метод открытого действия в ваших контроллерах можно протестировать в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="ee2cf-131">Щелкните имя метода, чтобы развернуть соответствующий раздел.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-131">Click a method name to expand the section.</span></span> <span data-ttu-id="ee2cf-132">Добавьте все необходимые параметры и нажмите кнопку **Попробуйте!** .</span><span class="sxs-lookup"><span data-stu-id="ee2cf-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Пример тестирования в Swagger метода GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="ee2cf-134">На снимках экрана используется пользовательский интерфейс Swagger версии 2.</span><span class="sxs-lookup"><span data-stu-id="ee2cf-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="ee2cf-135">Пример версии 3 см. в разделе [Пример Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="ee2cf-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ee2cf-136">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ee2cf-136">Next steps</span></span>

* [<span data-ttu-id="ee2cf-137">Начало работы с Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="ee2cf-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="ee2cf-138">Начало работы с NSwag</span><span class="sxs-lookup"><span data-stu-id="ee2cf-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
