<span data-ttu-id="510a2-101">Компонент (*app. Razor*) похож на компонент, `App` обнаруженный в приложениях блазор Server. `App`</span><span class="sxs-lookup"><span data-stu-id="510a2-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="510a2-102">`CascadingAuthenticationState` Компонент управляет предоставлением `AuthenticationState` для остального приложения.</span><span class="sxs-lookup"><span data-stu-id="510a2-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="510a2-103">`AuthorizeRouteView` Компонент гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом визуализирует `RedirectToLogin` компонент.</span><span class="sxs-lookup"><span data-stu-id="510a2-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="510a2-104">`RedirectToLogin` Компонент управляет перенаправлением неавторизованных пользователей на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="510a2-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
