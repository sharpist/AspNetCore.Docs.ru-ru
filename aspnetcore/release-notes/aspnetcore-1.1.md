---
title: Новые возможности ASP.NET Core 1.1
author: rick-anderson
description: Дополнительные сведения о новых возможностях ASP.NET Core 1.1.
ms.author: riande
ms.date: 12/18/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-1.1
ms.openlocfilehash: c3317c8ff905d1c7a9c9d9019d18cbebad92edaf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775847"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="d3f99-103">Новые возможности ASP.NET Core 1.1</span><span class="sxs-lookup"><span data-stu-id="d3f99-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="d3f99-104">В состав ASP.NET Core 1.1 входят следующие новые функции и компоненты:</span><span class="sxs-lookup"><span data-stu-id="d3f99-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="d3f99-105">ПО промежуточного слоя для переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="d3f99-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="d3f99-106">ПО промежуточного слоя для кэширования ответов</span><span class="sxs-lookup"><span data-stu-id="d3f99-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="d3f99-107">Просмотр компонентов как вспомогательных функций тегов</span><span class="sxs-lookup"><span data-stu-id="d3f99-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="d3f99-108">ПО промежуточного слоя в качестве фильтров MVC</span><span class="sxs-lookup"><span data-stu-id="d3f99-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="d3f99-109">Поставщик TempData на основе файлов cookie</span><span class="sxs-lookup"><span data-stu-id="d3f99-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="d3f99-110">Поставщик ведения журнала службы приложений Azure</span><span class="sxs-lookup"><span data-stu-id="d3f99-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="d3f99-111">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3f99-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="d3f99-112">Репозитории ключей защиты данных для хранилищ Azure и Redis</span><span class="sxs-lookup"><span data-stu-id="d3f99-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="d3f99-113">Сервер WebListener для Windows</span><span class="sxs-lookup"><span data-stu-id="d3f99-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="d3f99-114">Поддержка WebSocket</span><span class="sxs-lookup"><span data-stu-id="d3f99-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="d3f99-115">Выбор между версиями ASP.NET Core 1.0 и 1.1</span><span class="sxs-lookup"><span data-stu-id="d3f99-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="d3f99-116">ASP.NET Core 1.1 имеет более широкий набор возможностей, чем ASP.NET Core 1.0.</span><span class="sxs-lookup"><span data-stu-id="d3f99-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="d3f99-117">Как правило, мы рекомендуем использовать последнюю версию.</span><span class="sxs-lookup"><span data-stu-id="d3f99-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="d3f99-118">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="d3f99-118">Additional Information</span></span>

- [<span data-ttu-id="d3f99-119">Заметки о выпуске ASP.NET Core 1.1.0</span><span class="sxs-lookup"><span data-stu-id="d3f99-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="d3f99-120">Чтобы отслеживать ход работы и планы команды разработчиков ASP.NET Core, смотрите выпуски [ASP.NET Community Standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="d3f99-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
