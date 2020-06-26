---
title: Внешние поставщики проверки подлинности OAuth
author: rick-anderson
description: Обнаружение внешних поставщиков проверки подлинности OAuth, которые работают с ASP.NET Core приложениями.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/otherlogins
ms.openlocfilehash: 438b06dfa55a6b5bdd7b97516005e1f918f7b6ae
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406073"
---
# <a name="external-oauth-authentication-providers"></a>Внешние поставщики проверки подлинности OAuth

По [Рик Андерсон (](https://twitter.com/RickAndMSFT), [получения запись блога](https://github.com/rustd)и [авторы новитскии](https://github.com/01binary)

В следующем списке перечислены общие внешние поставщики проверки подлинности OAuth, работающие с ASP.NET Core приложениями. Сторонние пакеты NuGet, такие как службы, поддерживаемые [ASPNET-от участников сообщества](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), можно использовать для дополнения поставщиков проверки подлинности, реализованных группой ASP.NET Core.

* [LinkedIn](https://www.linkedin.com/developer/apps) ([инструкции](https://developer.linkedin.com/docs/oauth2))

* [Instagram](https://www.instagram.com/developer/register/) ([инструкции](https://www.instagram.com/developer/authentication/))

* [Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([инструкции](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))

* [GitHub](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([инструкции](https://developer.github.com/v3/oauth/))

* [Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([инструкции](https://developer.yahoo.com/bbauth/user.html))

* [Тумблр](https://www.tumblr.com/oauth/apps) ([инструкции](https://www.tumblr.com/docs/api/v2#auth))

* [Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([инструкции](https://developers.pinterest.com/docs/api/overview/?))

* [Pocket](https://getpocket.com/developer/apps/new) ([инструкции](https://getpocket.com/developer/docs/authentication))

* [Flickr](https://www.flickr.com/services/apps/create) ([инструкции](https://www.flickr.com/services/api/auth.oauth.html))

* [Дриббле](https://dribbble.com/signup) ([инструкции](https://developer.dribbble.com/v1/oauth/))

* [Vimeo](https://vimeo.com/join) ([инструкции](https://developer.vimeo.com/api/authentication))

* [Саундклауд](https://soundcloud.com/you/apps/new) ([инструкции](https://developers.soundcloud.com/blog/we-love-oauth-2))

* [VK](https://vk.com/apps?act=manage) ([инструкции](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
