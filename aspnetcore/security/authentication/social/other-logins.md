---
title: Внешние поставщики проверки подлинности OAuth
author: rick-anderson
description: Обнаружение внешних поставщиков проверки подлинности OAuth, которые работают с ASP.NET Core приложениями.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: c61bbef26017f14df09f8ca6f494d29f79903b6b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776465"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="20b88-103">Внешние поставщики проверки подлинности OAuth</span><span class="sxs-lookup"><span data-stu-id="20b88-103">External OAuth authentication providers</span></span>

<span data-ttu-id="20b88-104">По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [получения запись блога](https://github.com/rustd)и [авторы новитскии](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="20b88-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="20b88-105">В следующем списке перечислены общие внешние поставщики проверки подлинности OAuth, работающие с ASP.NET Core приложениями.</span><span class="sxs-lookup"><span data-stu-id="20b88-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="20b88-106">Сторонние пакеты NuGet, такие как службы, поддерживаемые [ASPNET-от участников сообщества](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), можно использовать для дополнения поставщиков проверки подлинности, реализованных группой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20b88-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="20b88-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([инструкции](https://developer.linkedin.com/docs/oauth2))</span><span class="sxs-lookup"><span data-stu-id="20b88-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="20b88-108">[Instagram](https://www.instagram.com/developer/register/) ([инструкции](https://www.instagram.com/developer/authentication/))</span><span class="sxs-lookup"><span data-stu-id="20b88-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="20b88-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([инструкции](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span><span class="sxs-lookup"><span data-stu-id="20b88-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="20b88-110">[GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([инструкции](https://developer.github.com/v3/oauth/))</span><span class="sxs-lookup"><span data-stu-id="20b88-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="20b88-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([инструкции](https://developer.yahoo.com/bbauth/user.html))</span><span class="sxs-lookup"><span data-stu-id="20b88-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="20b88-112">[Тумблр](https://www.tumblr.com/oauth/apps) ([инструкции](https://www.tumblr.com/docs/api/v2#auth))</span><span class="sxs-lookup"><span data-stu-id="20b88-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="20b88-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([инструкции](https://developers.pinterest.com/docs/api/overview/?))</span><span class="sxs-lookup"><span data-stu-id="20b88-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="20b88-114">[Pocket](https://getpocket.com/developer/apps/new) ([инструкции](https://getpocket.com/developer/docs/authentication))</span><span class="sxs-lookup"><span data-stu-id="20b88-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="20b88-115">[Flickr](https://www.flickr.com/services/apps/create) ([инструкции](https://www.flickr.com/services/api/auth.oauth.html))</span><span class="sxs-lookup"><span data-stu-id="20b88-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="20b88-116">[Дриббле](https://dribbble.com/signup) ([инструкции](https://developer.dribbble.com/v1/oauth/))</span><span class="sxs-lookup"><span data-stu-id="20b88-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="20b88-117">[Vimeo](https://vimeo.com/join) ([инструкции](https://developer.vimeo.com/api/authentication))</span><span class="sxs-lookup"><span data-stu-id="20b88-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="20b88-118">[Саундклауд](https://soundcloud.com/you/apps/new) ([инструкции](https://developers.soundcloud.com/blog/we-love-oauth-2))</span><span class="sxs-lookup"><span data-stu-id="20b88-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="20b88-119">[VK](https://vk.com/apps?act=manage) ([инструкции](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span><span class="sxs-lookup"><span data-stu-id="20b88-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
