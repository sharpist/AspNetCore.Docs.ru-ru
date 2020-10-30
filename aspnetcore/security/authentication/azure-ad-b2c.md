---
title: Проверка подлинности в облаке с помощью Azure Active Directory B2C в ASP.NET Core
author: camsoper
description: Узнайте, как настроить проверку подлинности Azure Active Directory B2C с помощью ASP.NET Core.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061396"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="ac216-103">Проверка подлинности в облаке с помощью Azure Active Directory B2C в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac216-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="ac216-104">Автор [Кэм Сопер (Cam Soper)](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="ac216-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="ac216-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) — это решение для управления удостоверениями в облаке для веб-и мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="ac216-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="ac216-106">Служба обеспечивает проверку подлинности для приложений, размещенных в облаке и в локальной среде.</span><span class="sxs-lookup"><span data-stu-id="ac216-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="ac216-107">Типы проверки подлинности включают отдельные учетные записи, учетные записи социальных сетей и Федеративные корпоративные учетные записи.</span><span class="sxs-lookup"><span data-stu-id="ac216-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="ac216-108">Кроме того, Azure AD B2C может предоставлять многофакторную проверку подлинности с минимальной конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="ac216-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="ac216-109">Azure Active Directory (Azure AD) и Azure AD B2C являются отдельными предложениями продуктов.</span><span class="sxs-lookup"><span data-stu-id="ac216-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="ac216-110">Клиент Azure AD представляет организацию, а Azure AD B2C клиент представляет коллекцию удостоверений для использования с приложениями проверяющей стороны.</span><span class="sxs-lookup"><span data-stu-id="ac216-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="ac216-111">Дополнительные сведения см. в разделе [Azure AD B2C: часто](/azure/active-directory-b2c/active-directory-b2c-faqs)задаваемые вопросы.</span><span class="sxs-lookup"><span data-stu-id="ac216-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="ac216-112">Из этого руководства вы узнаете, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="ac216-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac216-113">Создание клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="ac216-114">Регистрация приложения в Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="ac216-115">Создание ASP.NET Core веб-приложения, настроенного для использования клиента Azure AD B2C для проверки подлинности, с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac216-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="ac216-116">Настройка политик, управляющих поведением клиента Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ac216-117">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ac216-117">Prerequisites</span></span>

<span data-ttu-id="ac216-118">Для этого пошагового руководства необходимы следующие сведения.</span><span class="sxs-lookup"><span data-stu-id="ac216-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="ac216-119">Подписка Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="ac216-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="ac216-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="ac216-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="ac216-121">Создание клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="ac216-122">Создайте клиент Azure Active Directory B2C [, как описано в документации](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="ac216-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="ac216-123">При появлении запроса связывание клиента с подпиской Azure не является обязательным для этого руководства.</span><span class="sxs-lookup"><span data-stu-id="ac216-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="ac216-124">Регистрация приложения в Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="ac216-125">В созданном клиенте Azure AD B2C Зарегистрируйте приложение, выполнив [действия, описанные в документации](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) в разделе **Регистрация веб-приложения** .</span><span class="sxs-lookup"><span data-stu-id="ac216-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="ac216-126">Прерывать в разделе **Создание секрета клиента веб-приложения** .</span><span class="sxs-lookup"><span data-stu-id="ac216-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="ac216-127">Секрет клиента не требуется для работы с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="ac216-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="ac216-128">Используйте следующие значения.</span><span class="sxs-lookup"><span data-stu-id="ac216-128">Use the following values:</span></span>

| <span data-ttu-id="ac216-129">Параметр</span><span class="sxs-lookup"><span data-stu-id="ac216-129">Setting</span></span>                       | <span data-ttu-id="ac216-130">Значение</span><span class="sxs-lookup"><span data-stu-id="ac216-130">Value</span></span>                     | <span data-ttu-id="ac216-131">Примечания</span><span class="sxs-lookup"><span data-stu-id="ac216-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ac216-132">**Имя**</span><span class="sxs-lookup"><span data-stu-id="ac216-132">**Name**</span></span>                      | <span data-ttu-id="ac216-133">*&lt;имя приложения&gt;*</span><span class="sxs-lookup"><span data-stu-id="ac216-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="ac216-134">Введите **имя** приложения, которое описывает ваше приложение для потребителей.</span><span class="sxs-lookup"><span data-stu-id="ac216-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="ac216-135">**Включить веб-приложение или веб-интерфейс API**</span><span class="sxs-lookup"><span data-stu-id="ac216-135">**Include web app / web API**</span></span> | <span data-ttu-id="ac216-136">Да</span><span class="sxs-lookup"><span data-stu-id="ac216-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="ac216-137">**Разрешить неявный поток**</span><span class="sxs-lookup"><span data-stu-id="ac216-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="ac216-138">Да</span><span class="sxs-lookup"><span data-stu-id="ac216-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="ac216-139">**URL-адрес ответа**</span><span class="sxs-lookup"><span data-stu-id="ac216-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="ac216-140">URL-адреса ответа — это конечные точки, куда Azure AD B2C возвращает все токены, запрашиваемые вашим приложением.</span><span class="sxs-lookup"><span data-stu-id="ac216-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="ac216-141">Visual Studio предоставляет URL-адрес ответа для использования.</span><span class="sxs-lookup"><span data-stu-id="ac216-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="ac216-142">Пока введите, `https://localhost:44300/signin-oidc` чтобы заполнить форму.</span><span class="sxs-lookup"><span data-stu-id="ac216-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="ac216-143">**Универсальный код ресурса идентификатора приложения**</span><span class="sxs-lookup"><span data-stu-id="ac216-143">**App ID URI**</span></span>                | <span data-ttu-id="ac216-144">Не указывайте</span><span class="sxs-lookup"><span data-stu-id="ac216-144">Leave blank</span></span>               | <span data-ttu-id="ac216-145">Не требуется для работы с этим руководством.</span><span class="sxs-lookup"><span data-stu-id="ac216-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="ac216-146">**Включить собственный клиент**</span><span class="sxs-lookup"><span data-stu-id="ac216-146">**Include native client**</span></span>     | <span data-ttu-id="ac216-147">Нет</span><span class="sxs-lookup"><span data-stu-id="ac216-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="ac216-148">При настройке URL-адреса ответа, отличного от localhost, следует учитывать [ограничения, которые разрешены в списке URL-адресов ответа](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="ac216-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="ac216-149">После регистрации приложения отобразится список приложений в клиенте.</span><span class="sxs-lookup"><span data-stu-id="ac216-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="ac216-150">Выберите приложение, которое было только что зарегистрировано.</span><span class="sxs-lookup"><span data-stu-id="ac216-150">Select the app that was just registered.</span></span> <span data-ttu-id="ac216-151">Щелкните значок **копирования** справа от поля **идентификатор приложения** , чтобы скопировать его в буфер обмена.</span><span class="sxs-lookup"><span data-stu-id="ac216-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="ac216-152">В настоящее время в клиенте Azure AD B2C больше не может быть настроено ничего, но оставьте окно браузера открытым.</span><span class="sxs-lookup"><span data-stu-id="ac216-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="ac216-153">После создания ASP.NET Core приложения возникает дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="ac216-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="ac216-154">Создание приложения ASP.NET Core в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac216-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="ac216-155">Шаблон веб-приложения Visual Studio можно настроить для использования клиента Azure AD B2C для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="ac216-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="ac216-156">В Visual Studio сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ac216-156">In Visual Studio:</span></span>

1. <span data-ttu-id="ac216-157">Создайте новое веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac216-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="ac216-158">Выберите **веб-приложение** из списка шаблонов.</span><span class="sxs-lookup"><span data-stu-id="ac216-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="ac216-159">Нажмите кнопку **изменить проверку подлинности** .</span><span class="sxs-lookup"><span data-stu-id="ac216-159">Select the **Change Authentication** button.</span></span>
    
    ![Кнопка изменения проверки подлинности](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="ac216-161">В диалоговом окне **Изменение проверки подлинности** выберите **отдельные учетные записи пользователей** , а затем в раскрывающемся списке выберите **подключиться к существующему хранилищу пользователя в облаке** .</span><span class="sxs-lookup"><span data-stu-id="ac216-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Диалоговое окно изменения проверки подлинности](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="ac216-163">Заполните форму следующими значениями:</span><span class="sxs-lookup"><span data-stu-id="ac216-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="ac216-164">Параметр</span><span class="sxs-lookup"><span data-stu-id="ac216-164">Setting</span></span>                       | <span data-ttu-id="ac216-165">Значение</span><span class="sxs-lookup"><span data-stu-id="ac216-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="ac216-166">**Доменное имя**</span><span class="sxs-lookup"><span data-stu-id="ac216-166">**Domain Name**</span></span>               | <span data-ttu-id="ac216-167">*&lt;доменное имя клиента B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="ac216-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="ac216-168">**Идентификатор приложения**</span><span class="sxs-lookup"><span data-stu-id="ac216-168">**Application ID**</span></span>            | <span data-ttu-id="ac216-169">*&lt;Вставка идентификатора приложения из буфера обмена&gt;*</span><span class="sxs-lookup"><span data-stu-id="ac216-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="ac216-170">**Путь обратного вызова**</span><span class="sxs-lookup"><span data-stu-id="ac216-170">**Callback Path**</span></span>             | <span data-ttu-id="ac216-171">*&lt;использовать значение по умолчанию&gt;*</span><span class="sxs-lookup"><span data-stu-id="ac216-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="ac216-172">**Политика регистрации или входа**</span><span class="sxs-lookup"><span data-stu-id="ac216-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="ac216-173">**Сброс политики паролей**</span><span class="sxs-lookup"><span data-stu-id="ac216-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="ac216-174">**Изменение политики профиля**</span><span class="sxs-lookup"><span data-stu-id="ac216-174">**Edit profile policy**</span></span>       | <span data-ttu-id="ac216-175">*&lt;Оставьте пустым&gt;*</span><span class="sxs-lookup"><span data-stu-id="ac216-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="ac216-176">Выберите ссылку **Копировать** рядом с **URI ответа** , чтобы скопировать универсальный код ресурса (URI) ответа в буфер обмена.</span><span class="sxs-lookup"><span data-stu-id="ac216-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="ac216-177">Нажмите кнопку **ОК** , чтобы закрыть диалоговое окно **Изменение проверки подлинности** .</span><span class="sxs-lookup"><span data-stu-id="ac216-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="ac216-178">Нажмите кнопку **ОК** , чтобы создать веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="ac216-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="ac216-179">Завершение регистрации приложения B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-179">Finish the B2C app registration</span></span>

<span data-ttu-id="ac216-180">Вернитесь в окно браузера, в котором все еще открыты свойства приложения B2C.</span><span class="sxs-lookup"><span data-stu-id="ac216-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="ac216-181">Измените указанный ранее **URL-адрес временного ответа** на значение, скопированное из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac216-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="ac216-182">Нажмите кнопку **сохранить** в верхней части окна.</span><span class="sxs-lookup"><span data-stu-id="ac216-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="ac216-183">Если вы не скопировали URL-адрес ответа, используйте адрес HTTPS на вкладке "Отладка" в свойствах веб-проекта и добавьте значение **каллбаккпас** из *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="ac216-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="ac216-184">Настройка политик</span><span class="sxs-lookup"><span data-stu-id="ac216-184">Configure policies</span></span>

<span data-ttu-id="ac216-185">Выполните действия, описанные в Azure AD B2C документации, чтобы [создать политику регистрации или входа](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), а затем [Создайте политику сброса паролей](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="ac216-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="ac216-186">Используйте примеры значений, приведенные в документации по **:::no-loc(Identity)::: поставщикам** , **атрибутам регистрации** и **утверждениям приложений** .</span><span class="sxs-lookup"><span data-stu-id="ac216-186">Use the example values provided in the documentation for **:::no-loc(Identity)::: providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="ac216-187">Использование кнопки **выполнить сейчас** для проверки политик, как описано в документации, является необязательным.</span><span class="sxs-lookup"><span data-stu-id="ac216-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="ac216-188">Убедитесь, что имена политик в точности соответствуют описанию в документации, так как эти политики использовались в диалоговом окне **Изменение проверки подлинности** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ac216-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="ac216-189">Имена политик можно проверить в *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="ac216-189">The policy names can be verified in *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="ac216-190">Настройка базовых Опенидконнектоптионс/JwtBearer/ :::no-loc(Cookie)::: Options</span><span class="sxs-lookup"><span data-stu-id="ac216-190">Configure the underlying OpenIdConnectOptions/JwtBearer/:::no-loc(Cookie)::: options</span></span>

<span data-ttu-id="ac216-191">Чтобы настроить базовые параметры напрямую, используйте соответствующую константу схемы в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ac216-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<:::no-loc(Cookie):::AuthenticationOptions>(
    AzureAD[B2C]Defaults.:::no-loc(Cookie):::Scheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="ac216-192">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="ac216-192">Run the app</span></span>

<span data-ttu-id="ac216-193">В Visual Studio нажмите клавишу **F5** , чтобы создать и запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="ac216-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="ac216-194">После запуска веб-приложения выберите **принять** , чтобы принять использование :::no-loc(cookie)::: s (при появлении запроса), а затем выберите **Вход** .</span><span class="sxs-lookup"><span data-stu-id="ac216-194">After the web app launches, select **Accept** to accept the use of :::no-loc(cookie):::s (if prompted), and then select **Sign in** .</span></span>

![Вход в приложение](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="ac216-196">Браузер перенаправляется на клиент Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="ac216-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="ac216-197">Войдите с помощью существующей учетной записи (если она была создана при тестировании политик) или выберите **зарегистрироваться сейчас** , чтобы создать новую учетную запись.</span><span class="sxs-lookup"><span data-stu-id="ac216-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="ac216-198">**Забыт пароль?** ссылка используется для сброса забытого пароля.</span><span class="sxs-lookup"><span data-stu-id="ac216-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Вход в Azure Active Directory B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="ac216-200">После успешного входа в систему браузер перенаправляется в веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="ac216-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Успех](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="ac216-202">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="ac216-202">Next steps</span></span>

<span data-ttu-id="ac216-203">В этом руководстве вы узнали, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="ac216-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ac216-204">Создание клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="ac216-205">Регистрация приложения в Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="ac216-206">Создание ASP.NET Core веб-приложения, настроенного для использования клиента Azure AD B2C для проверки подлинности, с помощью Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ac216-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="ac216-207">Настройка политик, управляющих поведением клиента Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="ac216-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="ac216-208">Теперь, когда ASP.NET Core приложение настроено для использования Azure AD B2C для проверки подлинности, [атрибут авторизации](xref:security/authorization/simple) можно использовать для защиты приложения.</span><span class="sxs-lookup"><span data-stu-id="ac216-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="ac216-209">Продолжайте разработку приложения, изучая следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ac216-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="ac216-210">[Настройка пользовательского интерфейса Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="ac216-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="ac216-211">[Настройте требования к сложности пароля](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="ac216-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="ac216-212">[Включите многофакторную проверку подлинности](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="ac216-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="ac216-213">Настройте дополнительные поставщики удостоверений, такие как [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)и др.</span><span class="sxs-lookup"><span data-stu-id="ac216-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="ac216-214">[Используйте API Graph Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) для получения дополнительных сведений о пользователе, таких как членство в группе, из клиента Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="ac216-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="ac216-215">[Как защитить веб-API, построенный с помощью ASP.NET Core, используя Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span><span class="sxs-lookup"><span data-stu-id="ac216-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="ac216-216">[Руководство. предоставление доступа к веб-API ASP.NET с помощью Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="ac216-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
