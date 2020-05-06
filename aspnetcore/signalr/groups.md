---
title: Управление пользователями и группами вSignalR
author: bradygaster
description: Общие сведения о SignalR ASP.NET Core управлении пользователями и группами.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776302"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="1f43d-103">Управление пользователями и группами вSignalR</span><span class="sxs-lookup"><span data-stu-id="1f43d-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="1f43d-104">По [Бреннан Конрой](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1f43d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="1f43d-105">разрешает отправлять сообщения всем соединениям, связанным с конкретным пользователем, а также именованным группам подключений.</span><span class="sxs-lookup"><span data-stu-id="1f43d-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="1f43d-106">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Загрузка)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1f43d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="1f43d-107">Пользователи вSignalR</span><span class="sxs-lookup"><span data-stu-id="1f43d-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="1f43d-108">позволяет передавать сообщения всем соединениям, связанным с конкретным пользователем.</span><span class="sxs-lookup"><span data-stu-id="1f43d-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="1f43d-109">По умолчанию SignalR использует `ClaimTypes.NameIdentifier` из из, `ClaimsPrincipal` связанного с соединением, в качестве идентификатора пользователя.</span><span class="sxs-lookup"><span data-stu-id="1f43d-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="1f43d-110">Один пользователь может иметь несколько подключений к SignalR приложению.</span><span class="sxs-lookup"><span data-stu-id="1f43d-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="1f43d-111">Например, пользователь может подключаться к рабочему столу, а также по телефону.</span><span class="sxs-lookup"><span data-stu-id="1f43d-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="1f43d-112">Каждое устройство имеет отдельное SignalR соединение, но все они связаны с одним и тем же пользователем.</span><span class="sxs-lookup"><span data-stu-id="1f43d-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="1f43d-113">При отправке пользователю сообщения все соединения, связанные с этим пользователем, получают сообщение.</span><span class="sxs-lookup"><span data-stu-id="1f43d-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="1f43d-114">Доступ к идентификатору пользователя для подключения можно получить с помощью `Context.UserIdentifier` свойства в центре.</span><span class="sxs-lookup"><span data-stu-id="1f43d-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="1f43d-115">Отправьте сообщение определенному пользователю, передав идентификатор пользователя в `User` функцию в методе Hub, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1f43d-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="1f43d-116">В идентификаторе пользователя учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="1f43d-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="1f43d-117">Группы вSignalR</span><span class="sxs-lookup"><span data-stu-id="1f43d-117">Groups in SignalR</span></span>

<span data-ttu-id="1f43d-118">Группа — это коллекция соединений, связанных с именем.</span><span class="sxs-lookup"><span data-stu-id="1f43d-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="1f43d-119">Сообщения могут отправляться всем подключениям в группе.</span><span class="sxs-lookup"><span data-stu-id="1f43d-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="1f43d-120">Группы — это рекомендуемый способ отправки на подключение или несколько подключений, так как группы управляются приложением.</span><span class="sxs-lookup"><span data-stu-id="1f43d-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="1f43d-121">Соединение может быть членом нескольких групп.</span><span class="sxs-lookup"><span data-stu-id="1f43d-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="1f43d-122">Это делает группы идеальными для чего-то вроде приложения для разговора, где каждая комната может быть представлена группой.</span><span class="sxs-lookup"><span data-stu-id="1f43d-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="1f43d-123">Соединения можно добавлять в группы или удалять из них с помощью `AddToGroupAsync` методов `RemoveFromGroupAsync` и.</span><span class="sxs-lookup"><span data-stu-id="1f43d-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="1f43d-124">Членство в группе не сохраняется при повторном подключении подключения.</span><span class="sxs-lookup"><span data-stu-id="1f43d-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="1f43d-125">Соединение должно повторно присоединиться к группе, когда она будет восстановлена.</span><span class="sxs-lookup"><span data-stu-id="1f43d-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="1f43d-126">Невозможно подсчитать членов группы, так как эта информация недоступна, если приложение масштабируется до нескольких серверов.</span><span class="sxs-lookup"><span data-stu-id="1f43d-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="1f43d-127">Чтобы защитить доступ к ресурсам при использовании групп, используйте функции [проверки подлинности и авторизации](xref:signalr/authn-and-authz) в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f43d-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="1f43d-128">Если вы добавляете пользователей в группу только в том случае, если учетные данные действительны для этой группы, сообщения, отправленные в эту группу, будут отправляться только полномочным пользователям.</span><span class="sxs-lookup"><span data-stu-id="1f43d-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="1f43d-129">Однако группы не являются функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="1f43d-129">However, groups are not a security feature.</span></span> <span data-ttu-id="1f43d-130">Утверждения проверки подлинности имеют функции, которые не являются группами, например срок действия и отзыв.</span><span class="sxs-lookup"><span data-stu-id="1f43d-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="1f43d-131">Если разрешение пользователя на доступ к группе отменено, необходимо вручную определить его и удалить из группы.</span><span class="sxs-lookup"><span data-stu-id="1f43d-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="1f43d-132">В именах групп учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="1f43d-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="1f43d-133">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f43d-133">Related resources</span></span>

* [<span data-ttu-id="1f43d-134">Начало работы</span><span class="sxs-lookup"><span data-stu-id="1f43d-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1f43d-135">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="1f43d-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1f43d-136">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="1f43d-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
