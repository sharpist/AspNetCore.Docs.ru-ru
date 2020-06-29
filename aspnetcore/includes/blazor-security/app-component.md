Компонент `App` (`App.razor`) аналогичен компоненту `App`, который находится в приложениях Blazor Server.

* Компонент <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> управляет предоставлением <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> остальным приложениям.
* Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом работать с компонентом `RedirectToLogin`.
* Компонент `RedirectToLogin` управляет перенаправлением неавторизованных пользователей на страницу входа.

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
