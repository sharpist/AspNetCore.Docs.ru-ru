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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062458"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="5afc7-103">Документация по веб-API ASP.NET Core с использованием Swagger (OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="5afc7-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="5afc7-104">Авторы: [Кристоф Ниенабер (Christoph Nienaber)](https://twitter.com/zuckerthoben) и [Рико Сутер (Rico Suter)](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="5afc7-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="5afc7-105">Swagger (OpenAPI) — это не зависящая от языка спецификация для описания REST API.</span><span class="sxs-lookup"><span data-stu-id="5afc7-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="5afc7-106">Она позволяет компьютерам и пользователям лучше понять возможности REST API без прямого доступа к исходному коду.</span><span class="sxs-lookup"><span data-stu-id="5afc7-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="5afc7-107">Ее основные цели:</span><span class="sxs-lookup"><span data-stu-id="5afc7-107">Its main goals are to:</span></span>

* <span data-ttu-id="5afc7-108">свести к минимуму объем работ, необходимых для соединения отдельных служб;</span><span class="sxs-lookup"><span data-stu-id="5afc7-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="5afc7-109">сократить время, необходимое для точного документирования службы.</span><span class="sxs-lookup"><span data-stu-id="5afc7-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="5afc7-110">Две основные реализации OpenAPI для .NET — это [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) и [NSwag](https://github.com/RicoSuter/NSwag). См. следующие статьи:</span><span class="sxs-lookup"><span data-stu-id="5afc7-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="5afc7-111">Начало работы с Swashbuckle и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5afc7-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="5afc7-112">Начало работы с NSwag и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5afc7-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="5afc7-113">OpenAPI и Swagger</span><span class="sxs-lookup"><span data-stu-id="5afc7-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="5afc7-114">Проект Swagger был передан OpenAPI Initiative в 2015 году и с тех пор называется OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="5afc7-115">Оба имени взаимозаменяемы.</span><span class="sxs-lookup"><span data-stu-id="5afc7-115">Both names are used interchangeably.</span></span> <span data-ttu-id="5afc7-116">Но "OpenAPI" относится к спецификации.</span><span class="sxs-lookup"><span data-stu-id="5afc7-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="5afc7-117">"Swagger" относится к семейству с открытым исходным кодом и коммерческим продуктам от SmartBear, которые работают со спецификацией OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="5afc7-118">Последующие продукты с открытым кодом, такие как [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), также относятся к семейству Swagger несмотря на то, что они не выпускаются SmartBear.</span><span class="sxs-lookup"><span data-stu-id="5afc7-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="5afc7-119">Иными словами:</span><span class="sxs-lookup"><span data-stu-id="5afc7-119">In short:</span></span>

* <span data-ttu-id="5afc7-120">OpenAPI — это спецификация.</span><span class="sxs-lookup"><span data-stu-id="5afc7-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="5afc7-121">Swagger — это инструментарий, использующий спецификацию OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="5afc7-122">Например, OpenAPIGenerator и SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="5afc7-123">Спецификация OpenAPI (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="5afc7-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="5afc7-124">Спецификация OpenAPI — это документ, описывающий возможности API.</span><span class="sxs-lookup"><span data-stu-id="5afc7-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="5afc7-125">Документ основан на XML и заметках атрибутов в контроллерах и моделях.</span><span class="sxs-lookup"><span data-stu-id="5afc7-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="5afc7-126">Это основная часть потока OpenAPI, которая используется для управления такими инструментами, как SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="5afc7-127">По умолчанию он называется *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="5afc7-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="5afc7-128">Ниже приведен сокращенный пример спецификации OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="5afc7-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="5afc7-129">Пользовательский интерфейс Swagger</span><span class="sxs-lookup"><span data-stu-id="5afc7-129">Swagger UI</span></span>

<span data-ttu-id="5afc7-130">[Пользовательский интерфейс Swagger](https://swagger.io/swagger-ui/) обеспечивает пользовательский веб-интерфейс, предоставляющий сведения о службе с использованием созданной спецификации OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="5afc7-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="5afc7-131">Swashbuckle и NSwag включают встроенную версию пользовательского интерфейса Swagger, чтобы его можно было разместить в приложении ASP.NET Core, используя вызов регистрации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="5afc7-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="5afc7-132">Пользовательский веб-интерфейс выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5afc7-132">The web UI looks like this:</span></span>

![Пользовательский интерфейс Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="5afc7-134">Каждый метод открытого действия в ваших контроллерах можно протестировать в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="5afc7-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="5afc7-135">Выберите имя метода, чтобы развернуть соответствующий раздел.</span><span class="sxs-lookup"><span data-stu-id="5afc7-135">Select a method name to expand the section.</span></span> <span data-ttu-id="5afc7-136">Добавьте все необходимые параметры и выберите **Попробуйте!** .</span><span class="sxs-lookup"><span data-stu-id="5afc7-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Пример тестирования в Swagger метода GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="5afc7-138">На снимках экрана используется пользовательский интерфейс Swagger версии 2.</span><span class="sxs-lookup"><span data-stu-id="5afc7-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="5afc7-139">Пример версии 3 см. в разделе [Пример Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="5afc7-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="5afc7-140">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="5afc7-140">Next steps</span></span>

* [<span data-ttu-id="5afc7-141">Начало работы с Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="5afc7-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="5afc7-142">Начало работы с NSwag</span><span class="sxs-lookup"><span data-stu-id="5afc7-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
