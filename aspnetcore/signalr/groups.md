---
title: Управление пользователями и группами в SignalR
author: bradygaster
description: Общие сведения о ASP.NET Core SignalR управлении пользователями и группами.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: 0dfdf3a5eccd7462b675554e02fe4d2e166e8b92
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627563"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="1c07f-103">Управление пользователями и группами в SignalR</span><span class="sxs-lookup"><span data-stu-id="1c07f-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="1c07f-104">По [Бреннан Конрой](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1c07f-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="1c07f-105">SignalR разрешает отправлять сообщения всем соединениям, связанным с конкретным пользователем, а также именованным группам подключений.</span><span class="sxs-lookup"><span data-stu-id="1c07f-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="1c07f-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1c07f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="1c07f-107">Пользователи в SignalR</span><span class="sxs-lookup"><span data-stu-id="1c07f-107">Users in SignalR</span></span>

<span data-ttu-id="1c07f-108">Один пользователь в SignalR может иметь несколько подключений к приложению.</span><span class="sxs-lookup"><span data-stu-id="1c07f-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="1c07f-109">Например, пользователь может подключаться к рабочему столу, а также по телефону.</span><span class="sxs-lookup"><span data-stu-id="1c07f-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="1c07f-110">Каждое устройство имеет отдельное SignalR соединение, но все они связаны с одним и тем же пользователем.</span><span class="sxs-lookup"><span data-stu-id="1c07f-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="1c07f-111">При отправке пользователю сообщения все соединения, связанные с этим пользователем, получают сообщение.</span><span class="sxs-lookup"><span data-stu-id="1c07f-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="1c07f-112">Доступ к идентификатору пользователя для соединения может осуществляться `Context.UserIdentifier` свойством в концентраторе.</span><span class="sxs-lookup"><span data-stu-id="1c07f-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="1c07f-113">По умолчанию SignalR использует `ClaimTypes.NameIdentifier` из из, `ClaimsPrincipal` связанного с соединением, в качестве идентификатора пользователя.</span><span class="sxs-lookup"><span data-stu-id="1c07f-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="1c07f-114">Сведения о настройке этого поведения см. в разделе [использование утверждений для настройки обработки удостоверений](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="1c07f-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="1c07f-115">Отправьте сообщение определенному пользователю, передав идентификатор пользователя в `User` функцию в методе концентратора, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1c07f-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="1c07f-116">В идентификаторе пользователя учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="1c07f-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="1c07f-117">Группы в SignalR</span><span class="sxs-lookup"><span data-stu-id="1c07f-117">Groups in SignalR</span></span>

<span data-ttu-id="1c07f-118">Группа — это коллекция соединений, связанных с именем.</span><span class="sxs-lookup"><span data-stu-id="1c07f-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="1c07f-119">Сообщения могут отправляться всем подключениям в группе.</span><span class="sxs-lookup"><span data-stu-id="1c07f-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="1c07f-120">Группы — это рекомендуемый способ отправки на подключение или несколько подключений, так как группы управляются приложением.</span><span class="sxs-lookup"><span data-stu-id="1c07f-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="1c07f-121">Соединение может быть членом нескольких групп.</span><span class="sxs-lookup"><span data-stu-id="1c07f-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="1c07f-122">Группы идеально подходят для приложения разговора, где каждая комната может быть представлена группой.</span><span class="sxs-lookup"><span data-stu-id="1c07f-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="1c07f-123">Соединения добавляются в группы или удаляются из групп с помощью `AddToGroupAsync` `RemoveFromGroupAsync` методов и.</span><span class="sxs-lookup"><span data-stu-id="1c07f-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="1c07f-124">Членство в группе не сохраняется при повторном подключении подключения.</span><span class="sxs-lookup"><span data-stu-id="1c07f-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="1c07f-125">Соединение должно повторно присоединиться к группе, когда она будет восстановлена.</span><span class="sxs-lookup"><span data-stu-id="1c07f-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="1c07f-126">Невозможно подсчитать членов группы, так как эта информация недоступна, если приложение масштабируется до нескольких серверов.</span><span class="sxs-lookup"><span data-stu-id="1c07f-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="1c07f-127">Чтобы защитить доступ к ресурсам при использовании групп, используйте функции [проверки подлинности и авторизации](xref:signalr/authn-and-authz) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c07f-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="1c07f-128">Если пользователь добавлен в группу только в том случае, если учетные данные действительны для этой группы, сообщения, отправленные в эту группу, будут отправляться только полномочным пользователям.</span><span class="sxs-lookup"><span data-stu-id="1c07f-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="1c07f-129">Однако группы не являются функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="1c07f-129">However, groups are not a security feature.</span></span> <span data-ttu-id="1c07f-130">Утверждения проверки подлинности имеют функции, которые не являются группами, например срок действия и отзыв.</span><span class="sxs-lookup"><span data-stu-id="1c07f-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="1c07f-131">Если разрешение пользователя на доступ к группе отменено, приложение должно явным образом удалить пользователя из группы.</span><span class="sxs-lookup"><span data-stu-id="1c07f-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="1c07f-132">В именах групп учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="1c07f-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="1c07f-133">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1c07f-133">Related resources</span></span>

* [<span data-ttu-id="1c07f-134">Начало работы</span><span class="sxs-lookup"><span data-stu-id="1c07f-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1c07f-135">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="1c07f-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1c07f-136">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="1c07f-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
