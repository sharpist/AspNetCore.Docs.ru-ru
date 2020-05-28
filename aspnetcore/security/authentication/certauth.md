---
<span data-ttu-id="5d247-101">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5d247-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5d247-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5d247-102">'Blazor'</span></span>
- <span data-ttu-id="5d247-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5d247-103">'Identity'</span></span>
- <span data-ttu-id="5d247-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5d247-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5d247-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5d247-105">'Razor'</span></span>
- <span data-ttu-id="5d247-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5d247-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="5d247-107">Настройка проверки подлинности сертификата в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d247-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="5d247-108">`Microsoft.AspNetCore.Authentication.Certificate`содержит реализацию, похожую на [проверку подлинности с помощью сертификата](https://tools.ietf.org/html/rfc5246#section-7.4.4) для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d247-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="5d247-109">Проверка подлинности сертификата выполняется на уровне TLS, прежде чем он когда-либо получит ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d247-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="5d247-110">Точнее, это обработчик проверки подлинности, который проверяет сертификат, а затем предоставляет событие, в котором можно разрешить этот сертификат в `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="5d247-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="5d247-111">[Настройте узел](#configure-your-host-to-require-certificates) для проверки подлинности на основе сертификата, как IIS, Kestrel, веб-приложения Azure или любое другое приложение, которое вы используете.</span><span class="sxs-lookup"><span data-stu-id="5d247-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="5d247-112">Сценарии прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="5d247-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="5d247-113">Проверка подлинности с помощью сертификата — это сценарий с отслеживанием состояния, который в основном используется, когда прокси или балансировщик нагрузки не обрабатывает трафик между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="5d247-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="5d247-114">Если используется прокси-сервер или балансировщик нагрузки, проверка подлинности сертификата работает только в том случае, если прокси-сервер или балансировщик нагрузки:</span><span class="sxs-lookup"><span data-stu-id="5d247-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="5d247-115">Обрабатывает проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="5d247-115">Handles the authentication.</span></span>
* <span data-ttu-id="5d247-116">Передает сведения о проверке подлинности пользователя в приложение (например, в заголовке запроса), которое действует на данные проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5d247-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="5d247-117">Альтернативой для проверки подлинности на сертификат в средах, где используются прокси-серверы и подсистемы балансировки нагрузки, являются Active Directory Федеративные службы (ADFS) с OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="5d247-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="5d247-118">Начало работы</span><span class="sxs-lookup"><span data-stu-id="5d247-118">Get started</span></span>

<span data-ttu-id="5d247-119">Получите HTTPS сертификат, примените его и [Настройте узел](#configure-your-host-to-require-certificates) так, чтобы он затребовал сертификаты.</span><span class="sxs-lookup"><span data-stu-id="5d247-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="5d247-120">В веб-приложении добавьте ссылку на `Microsoft.AspNetCore.Authentication.Certificate` пакет.</span><span class="sxs-lookup"><span data-stu-id="5d247-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="5d247-121">Затем в `Startup.ConfigureServices` методе вызовите `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` с вашими параметрами, предоставив делегат для, `OnCertificateValidated` чтобы выполнить дополнительную проверку сертификата клиента, отправляемого с запросами.</span><span class="sxs-lookup"><span data-stu-id="5d247-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="5d247-122">Включите эту информацию в `ClaimsPrincipal` и установите ее в `context.Principal` свойстве.</span><span class="sxs-lookup"><span data-stu-id="5d247-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="5d247-123">Если проверка подлинности завершается неудачно, этот обработчик возвращает `403 (Forbidden)` ответ, а не `401 (Unauthorized)` , как вы можете ожидать.</span><span class="sxs-lookup"><span data-stu-id="5d247-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="5d247-124">Причина заключается в том, что проверка подлинности должна выполняться во время первоначального TLS-подключения.</span><span class="sxs-lookup"><span data-stu-id="5d247-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="5d247-125">К моменту, когда он достигает обработчика, он слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="5d247-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="5d247-126">Невозможно обновить подключение между анонимным подключением и сертификатом.</span><span class="sxs-lookup"><span data-stu-id="5d247-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="5d247-127">Кроме того `app.UseAuthentication();` , добавьте в `Startup.Configure` метод.</span><span class="sxs-lookup"><span data-stu-id="5d247-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="5d247-128">В противном случае `HttpContext.User` не будет задано значение, `ClaimsPrincipal` созданное на основе сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="5d247-129">Пример:</span><span class="sxs-lookup"><span data-stu-id="5d247-129">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="5d247-130">В предыдущем примере показан способ добавления проверки подлинности с помощью сертификата по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d247-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="5d247-131">Обработчик конструирует субъект-пользователя, используя общие свойства сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="5d247-132">Настройка проверки сертификата</span><span class="sxs-lookup"><span data-stu-id="5d247-132">Configure certificate validation</span></span>

<span data-ttu-id="5d247-133">`CertificateAuthenticationOptions`Обработчик содержит некоторые встроенные проверки, которые являются минимальными проверками, которые следует выполнить с сертификатом.</span><span class="sxs-lookup"><span data-stu-id="5d247-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="5d247-134">Каждый из этих параметров включен по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d247-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="5d247-135">Алловедцертификатетипес = цепочка, Селфсигнед или все (цепочка | Селфсигнед)</span><span class="sxs-lookup"><span data-stu-id="5d247-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="5d247-136">Значение по умолчанию — `CertificateTypes.Chained`.</span><span class="sxs-lookup"><span data-stu-id="5d247-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="5d247-137">Эта проверка подтверждает, что разрешен только соответствующий тип сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="5d247-138">Если приложение использует самозаверяющие сертификаты, для этого параметра необходимо задать значение `CertificateTypes.All` или `CertificateTypes.SelfSigned` .</span><span class="sxs-lookup"><span data-stu-id="5d247-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="5d247-139">валидатецертификатеусе</span><span class="sxs-lookup"><span data-stu-id="5d247-139">ValidateCertificateUse</span></span>

<span data-ttu-id="5d247-140">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="5d247-140">Default value: `true`</span></span>

<span data-ttu-id="5d247-141">Эта проверка проверяет, что сертификат, предоставленный клиентом, имеет расширенное использование ключа проверки подлинности клиента (EKU) или вообще не содержит EKU.</span><span class="sxs-lookup"><span data-stu-id="5d247-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="5d247-142">Как говорится в спецификации, если EKU не указано, все EKU считаются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="5d247-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="5d247-143">валидатевалидитипериод</span><span class="sxs-lookup"><span data-stu-id="5d247-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="5d247-144">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="5d247-144">Default value: `true`</span></span>

<span data-ttu-id="5d247-145">Эта проверка проверяет, что сертификат находится в пределах срока действия.</span><span class="sxs-lookup"><span data-stu-id="5d247-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="5d247-146">В каждом запросе обработчик гарантирует, что сертификат, который был действителен, когда он был предоставлен, не истечет в течение текущего сеанса.</span><span class="sxs-lookup"><span data-stu-id="5d247-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="5d247-147">ревокатионфлаг</span><span class="sxs-lookup"><span data-stu-id="5d247-147">RevocationFlag</span></span>

<span data-ttu-id="5d247-148">Значение по умолчанию — `X509RevocationFlag.ExcludeRoot`.</span><span class="sxs-lookup"><span data-stu-id="5d247-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="5d247-149">Флаг, указывающий, какие сертификаты в цепочке проверяются на отзыв.</span><span class="sxs-lookup"><span data-stu-id="5d247-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="5d247-150">Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.</span><span class="sxs-lookup"><span data-stu-id="5d247-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="5d247-151">ревокатионмоде</span><span class="sxs-lookup"><span data-stu-id="5d247-151">RevocationMode</span></span>

<span data-ttu-id="5d247-152">Значение по умолчанию — `X509RevocationMode.Online`.</span><span class="sxs-lookup"><span data-stu-id="5d247-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="5d247-153">Флаг, указывающий, как выполняются проверки отзыва.</span><span class="sxs-lookup"><span data-stu-id="5d247-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="5d247-154">Указание проверки в сети может привести к длительной задержке при обращении к центру сертификации.</span><span class="sxs-lookup"><span data-stu-id="5d247-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="5d247-155">Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.</span><span class="sxs-lookup"><span data-stu-id="5d247-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="5d247-156">Можно ли настроить в приложении требование сертификата только для определенных путей?</span><span class="sxs-lookup"><span data-stu-id="5d247-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="5d247-157">Это невозможно.</span><span class="sxs-lookup"><span data-stu-id="5d247-157">This isn't possible.</span></span> <span data-ttu-id="5d247-158">Помните, что обмен сертификатами выполняется в начале диалога HTTPS, он выполняется сервером до получения первого запроса на это подключение, поэтому невозможно ограничить область на основе каких-либо полей запроса.</span><span class="sxs-lookup"><span data-stu-id="5d247-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="5d247-159">События обработчика</span><span class="sxs-lookup"><span data-stu-id="5d247-159">Handler events</span></span>

<span data-ttu-id="5d247-160">Обработчик имеет два события:</span><span class="sxs-lookup"><span data-stu-id="5d247-160">The handler has two events:</span></span>

* <span data-ttu-id="5d247-161">`OnAuthenticationFailed`: Вызывается, если исключение происходит во время проверки подлинности и позволяет реагировать.</span><span class="sxs-lookup"><span data-stu-id="5d247-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="5d247-162">`OnCertificateValidated`: Вызывается после проверки сертификата, прошел проверку и создается участник по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d247-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="5d247-163">Это событие позволяет выполнять собственную проверку и дополнять или заменять субъект.</span><span class="sxs-lookup"><span data-stu-id="5d247-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="5d247-164">Ниже приведены примеры.</span><span class="sxs-lookup"><span data-stu-id="5d247-164">For examples include:</span></span>
  * <span data-ttu-id="5d247-165">Определение, известен ли сертификат службам.</span><span class="sxs-lookup"><span data-stu-id="5d247-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="5d247-166">Создание собственного участника.</span><span class="sxs-lookup"><span data-stu-id="5d247-166">Constructing your own principal.</span></span> <span data-ttu-id="5d247-167">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5d247-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="5d247-168">Если входящий сертификат не соответствует дополнительной проверке, позвоните `context.Fail("failure reason")` по причине сбоя.</span><span class="sxs-lookup"><span data-stu-id="5d247-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="5d247-169">Для реальной функциональности вам, вероятно, потребуется вызвать службу, зарегистрированную в внедрении зависимостей, которая подключается к базе данных или другому хранилищу пользователей.</span><span class="sxs-lookup"><span data-stu-id="5d247-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="5d247-170">Получите доступ к службе с помощью контекста, переданного в делегат.</span><span class="sxs-lookup"><span data-stu-id="5d247-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="5d247-171">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5d247-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="5d247-172">По сути, проверка сертификата является проблемой авторизации.</span><span class="sxs-lookup"><span data-stu-id="5d247-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="5d247-173">Добавление проверки, например, издателя или отпечатка в политике авторизации, а не внутри `OnCertificateValidated` , вполне приемлемо.</span><span class="sxs-lookup"><span data-stu-id="5d247-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="5d247-174">Настройка узла для использования сертификатов</span><span class="sxs-lookup"><span data-stu-id="5d247-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="5d247-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5d247-175">Kestrel</span></span>

<span data-ttu-id="5d247-176">В *Program.CS*настройте Kestrel следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5d247-176">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="5d247-177">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d247-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="5d247-178">IIS</span><span class="sxs-lookup"><span data-stu-id="5d247-178">IIS</span></span>

<span data-ttu-id="5d247-179">В диспетчере служб IIS выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="5d247-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="5d247-180">Выберите сайт на вкладке " **подключения** ".</span><span class="sxs-lookup"><span data-stu-id="5d247-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="5d247-181">Дважды щелкните параметр **Параметры SSL** в окне **просмотра функций** .</span><span class="sxs-lookup"><span data-stu-id="5d247-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="5d247-182">Установите флажок **требовать SSL** и установите переключатель **требуется** в разделе **Сертификаты клиента** .</span><span class="sxs-lookup"><span data-stu-id="5d247-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Параметры сертификата клиента в службах IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="5d247-184">Azure и настраиваемые веб-прокси</span><span class="sxs-lookup"><span data-stu-id="5d247-184">Azure and custom web proxies</span></span>

<span data-ttu-id="5d247-185">Сведения о настройке по промежуточного слоя для переадресации сертификатов см. в [документации по узлу и развертыванию](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .</span><span class="sxs-lookup"><span data-stu-id="5d247-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="5d247-186">Использование проверки подлинности сертификата в веб-приложениях Azure</span><span class="sxs-lookup"><span data-stu-id="5d247-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="5d247-187">Для Azure Настройка пересылки не требуется.</span><span class="sxs-lookup"><span data-stu-id="5d247-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="5d247-188">Это уже настроено в по промежуточного слоя на пересылку сертификатов.</span><span class="sxs-lookup"><span data-stu-id="5d247-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="5d247-189">Для этого необходимо наличие Цертификатефорвардингмиддлеваре.</span><span class="sxs-lookup"><span data-stu-id="5d247-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="5d247-190">Использование проверки подлинности сертификатов в пользовательских веб-прокси</span><span class="sxs-lookup"><span data-stu-id="5d247-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="5d247-191">`AddCertificateForwarding`Метод используется для указания:</span><span class="sxs-lookup"><span data-stu-id="5d247-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="5d247-192">Имя заголовка клиента.</span><span class="sxs-lookup"><span data-stu-id="5d247-192">The client header name.</span></span>
* <span data-ttu-id="5d247-193">Способ загрузки сертификата (с помощью `HeaderConverter` Свойства).</span><span class="sxs-lookup"><span data-stu-id="5d247-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="5d247-194">В пользовательских веб-прокси сертификат передается в виде пользовательского заголовка запроса, например `X-SSL-CERT` .</span><span class="sxs-lookup"><span data-stu-id="5d247-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="5d247-195">Чтобы использовать его, Настройте переадресацию сертификатов в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5d247-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="5d247-196">`Startup.Configure`Затем метод добавляет по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="5d247-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="5d247-197">`UseCertificateForwarding`вызывается перед вызовами функций `UseAuthentication` и `UseAuthorization` :</span><span class="sxs-lookup"><span data-stu-id="5d247-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="5d247-198">Для реализации логики проверки можно использовать отдельный класс.</span><span class="sxs-lookup"><span data-stu-id="5d247-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="5d247-199">Так как в этом примере используется тот же самозаверяющий сертификат, убедитесь, что только ваш сертификат можно использовать.</span><span class="sxs-lookup"><span data-stu-id="5d247-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="5d247-200">Убедитесь, что отпечатки сертификата клиента и сертификата сервера совпадают, в противном случае любой сертификат может быть использован и будет достаточно для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5d247-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="5d247-201">Он будет использоваться внутри `AddCertificate` метода.</span><span class="sxs-lookup"><span data-stu-id="5d247-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="5d247-202">Вы также можете проверить субъект или его издателя, если вы используете промежуточные или дочерние сертификаты.</span><span class="sxs-lookup"><span data-stu-id="5d247-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="5d247-203">Реализация HttpClient с помощью сертификата и HttpClientHandler</span><span class="sxs-lookup"><span data-stu-id="5d247-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="5d247-204">HttpClientHandler можно добавить непосредственно в конструктор класса HttpClient.</span><span class="sxs-lookup"><span data-stu-id="5d247-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="5d247-205">При создании экземпляров HttpClient следует соблюдать осторожность.</span><span class="sxs-lookup"><span data-stu-id="5d247-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="5d247-206">Затем HttpClient будет отсылать сертификат с каждым запросом.</span><span class="sxs-lookup"><span data-stu-id="5d247-206">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="5d247-207">Реализация HttpClient с помощью сертификата и именованного HttpClient из Ихттпклиентфактори</span><span class="sxs-lookup"><span data-stu-id="5d247-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="5d247-208">В следующем примере сертификат клиента добавляется в HttpClientHandler с помощью свойства ClientCertificates из обработчика.</span><span class="sxs-lookup"><span data-stu-id="5d247-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="5d247-209">Затем этот обработчик можно использовать в именованном экземпляре HttpClient с помощью метода Конфигурепримарихттпмессажехандлер.</span><span class="sxs-lookup"><span data-stu-id="5d247-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="5d247-210">Это программа установки в классе Startup в методе ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="5d247-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="5d247-211">Затем Ихттпклиентфактори можно использовать для получения именованного экземпляра с обработчиком и сертификатом.</span><span class="sxs-lookup"><span data-stu-id="5d247-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="5d247-212">Для получения экземпляра используется метод Креатеклиент с именем клиента, определенного в классе Startup.</span><span class="sxs-lookup"><span data-stu-id="5d247-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="5d247-213">HTTP-запрос может быть отправлен с помощью клиента по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="5d247-213">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="5d247-214">Если на сервер отправляется правильный сертификат, возвращаются данные.</span><span class="sxs-lookup"><span data-stu-id="5d247-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="5d247-215">Если сертификат или неправильный сертификат не отправляется, возвращается код состояния HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="5d247-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="5d247-216">Создание сертификатов в PowerShell</span><span class="sxs-lookup"><span data-stu-id="5d247-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="5d247-217">Создание сертификатов — самая сложная часть настройки этого потока.</span><span class="sxs-lookup"><span data-stu-id="5d247-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="5d247-218">Корневой сертификат можно создать с помощью `New-SelfSignedCertificate` командлета PowerShell.</span><span class="sxs-lookup"><span data-stu-id="5d247-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="5d247-219">При создании сертификата используйте надежный пароль.</span><span class="sxs-lookup"><span data-stu-id="5d247-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="5d247-220">Важно добавить `KeyUsageProperty` параметр и `KeyUsage` параметр, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="5d247-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="5d247-221">Создать корневой ЦС</span><span class="sxs-lookup"><span data-stu-id="5d247-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="5d247-222">`-DnsName`Значение параметра должно соответствовать целевому объекту развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="5d247-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="5d247-223">Например, "localhost" для разработки.</span><span class="sxs-lookup"><span data-stu-id="5d247-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="5d247-224">Установить в доверенном корневом каталоге</span><span class="sxs-lookup"><span data-stu-id="5d247-224">Install in the trusted root</span></span>

<span data-ttu-id="5d247-225">Корневой сертификат должен быть доверенным в системе узла.</span><span class="sxs-lookup"><span data-stu-id="5d247-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="5d247-226">Корневой сертификат, который не был создан центром сертификации, не будет доверенным по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5d247-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="5d247-227">Следующая ссылка объясняет, как это можно сделать в Windows:</span><span class="sxs-lookup"><span data-stu-id="5d247-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="5d247-228">Промежуточный сертификат</span><span class="sxs-lookup"><span data-stu-id="5d247-228">Intermediate certificate</span></span>

<span data-ttu-id="5d247-229">Теперь промежуточный сертификат можно создать на основе корневого сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="5d247-230">Это не является обязательным для всех вариантов использования, но может потребоваться создать несколько сертификатов или необходимо активировать или отключить группы сертификатов.</span><span class="sxs-lookup"><span data-stu-id="5d247-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="5d247-231">`TextExtension`Параметр необходим для задания длины пути в базовых ограничениях сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="5d247-232">Затем промежуточный сертификат можно добавить в доверенный промежуточный сертификат в системе узла Windows.</span><span class="sxs-lookup"><span data-stu-id="5d247-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="5d247-233">Создать дочерний сертификат из промежуточного сертификата</span><span class="sxs-lookup"><span data-stu-id="5d247-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="5d247-234">Дочерний сертификат можно создать из промежуточного сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="5d247-235">Это конечная сущность, и ей не нужно создавать больше дочерних сертификатов.</span><span class="sxs-lookup"><span data-stu-id="5d247-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="5d247-236">Создать дочерний сертификат из корневого сертификата</span><span class="sxs-lookup"><span data-stu-id="5d247-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="5d247-237">Дочерний сертификат можно также создать непосредственно из корневого сертификата.</span><span class="sxs-lookup"><span data-stu-id="5d247-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="5d247-238">Пример корневого промежуточного сертификата — сертификат</span><span class="sxs-lookup"><span data-stu-id="5d247-238">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="5d247-239">При использовании корневого, промежуточного или дочернего сертификата сертификаты можно проверять с помощью отпечатка или PublicKey по мере необходимости.</span><span class="sxs-lookup"><span data-stu-id="5d247-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
