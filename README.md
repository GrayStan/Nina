﻿# Nina WebAPI

## Setup
In Global.asax, set:

```C#
public static void RegisterRoutes(RouteCollection routes)
{
    routes.Add(new Route("{resource}.axd/{*pathInfo}", new StopRoutingHandler()));
    routes.Add(new Nina.MountingPoint("~/api", null));
}

protected void Application_Start()
{
    RegisterRoutes(RouteTable.Routes);
}
```

## Modules

Modules class must inherit from `Nina.Module`. Decorete class with `[BaseUrl]` for set base url.

```C#
[BaseUrl("/user")]
pubic class UserApi : Nina.Module
{
    [Get("/{id}")]
    public User GetUser(int id)
    {
    }

    [Post("/")]
    public int Post(UserView user)
    {
    }
    
    [Get("/code/{id:(\\[a-z]{8})}")]
    public string GetCode(string id)
    {
    }
}
```

## Model Binding

Parameters bind rules:

- If parameter name found on route pattern? Use route value (`/edit/{id}`)
- If parameter name not found on route:
    - Parameter type is `String`? Use `Request.Form.ToString()`
    - Parameter type is `NameValueCollection`? Use `Request.Params` = Form + QueryString + Cookies + ServerVariables
    - Parameter type is `Stream`? Use `Request.InputStream`
    - Parameter type is `HttpFileCollection`? Use `Request.Files`
    - Parameter type is `Object`? Use `JsonDeserialize` (can be a `JObject` too)
    - Model is `Null`? Use `default(T)`

## Result actions

- `JsonResult` - Returns data as JSON string. Is default result when method returns a non `BaseResult`
- `ErrorResult` - Returns an HTTP error in JSON format
- `FileContentResult` - Returns a file from a stream
- `FileResult` - Returns a file from disk
- `JsonPResult` - Returns JSON string with callback() function

## Authentication & authorization

- Use `[Authorize]` attribute - checks `HttpContext.User.Identity.IsAuthenticated`
- Use `[Role("admin", "user")]` attribute - checkes `HttpContext.User.IsInRole()`
