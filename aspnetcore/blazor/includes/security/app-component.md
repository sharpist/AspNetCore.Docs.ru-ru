<span data-ttu-id="a1699-101">Компонент `App` (`App.razor`) аналогичен компоненту `App`, который находится в приложениях Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="a1699-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="a1699-102">Компонент <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> управляет предоставлением <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> остальным приложениям.</span><span class="sxs-lookup"><span data-stu-id="a1699-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="a1699-103">Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом работать с компонентом `RedirectToLogin`.</span><span class="sxs-lookup"><span data-stu-id="a1699-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="a1699-104">Компонент `RedirectToLogin` управляет перенаправлением неавторизованных пользователей на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="a1699-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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

[!include[](../prefer-exact-matches.md)]
