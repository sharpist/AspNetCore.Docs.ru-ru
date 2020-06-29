<span data-ttu-id="172c1-101">Страница индекса (`wwwroot/index.html`) содержит сценарий, определяющий `AuthenticationService` в JavaScript.</span><span class="sxs-lookup"><span data-stu-id="172c1-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="172c1-102">`AuthenticationService` обрабатывает низкоуровневые сведения о протоколе OIDC.</span><span class="sxs-lookup"><span data-stu-id="172c1-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="172c1-103">Приложение внутренне вызывает методы, определенные в сценарии для выполнения операций проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="172c1-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
