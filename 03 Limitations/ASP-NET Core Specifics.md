# ASP.NET Core Specifics

Native AOT in ASP.NET Core requires a shift from the "battery-included" defaults to a "pick-what-you-use" approach.

## Supported Application Models

### Fully Supported
*   **Minimal APIs**: The primary citizen for AOT.
*   **gRPC**: Fully supported.
*   **Worker Services**: Fully supported.

### Partially Supported
*   **SignalR**: Trimming and Native AOT support was added in **.NET 9** for both client and server scenarios. However, **strongly typed hubs (`Hub<T>`) are not supported** with `PublishAot` — they rely on `Reflection.Emit` to generate proxy implementations at runtime, which will cause runtime exceptions.

    **Workaround**: Use the non-generic `Hub` base class with `SendAsync`. You can use `nameof()` to maintain some type safety:
    ```csharp
    // Define the client interface for documentation/sharing
    public interface IChatClient
    {
        Task ReceiveMessage(string user, string message);
    }

    // Use Hub (not Hub<T>) for AOT compatibility
    public class ChatHub : Hub
    {
        public async Task SendMessage(string user, string message)
            => await Clients.All.SendAsync(nameof(IChatClient.ReceiveMessage), user, message);
    }
    ```

    For client-side strong typing, the community library [TypedSignalR.Client](https://github.com/nenoNaninu/TypedSignalR.Client) uses source generators and is AOT-compatible.

### Supported Middleware & Features
The following ASP.NET Core features are fully compatible with Native AOT:
*   JWT Authentication
*   CORS
*   HealthChecks
*   HttpLogging
*   Localization
*   OutputCaching
*   RateLimiting
*   RequestDecompression
*   ResponseCaching
*   ResponseCompression
*   Rewrite
*   StaticFiles
*   WebSockets

## Unsupported Features
The following are **NOT** supported in Native AOT:
*   **MVC / Razor Pages**: Anything relying on runtime compilation of views (`.cshtml`) or runtime discovery of Controllers.
*   **Blazor Server**: Not supported.
*   **Session**: Not supported.
*   **SPA (Single Page Application middleware)**: Not supported.
*   **Non-JWT Authentication**: Other authentication schemes may not be compatible.
*   **`UseStartup<T>`**: The Startup class pattern is not compatible. You must use `Program.cs` and the minimal hosting model.

## The `CreateSlimBuilder`

For AOT, you should use `WebApplication.CreateSlimBuilder(args)` instead of `CreateBuilder(args)`. It initializes only the minimum ASP.NET Core features necessary to run an app.

### What `CreateSlimBuilder` includes:
*   JSON file configuration (`appsettings.json` and `appsettings.{EnvironmentName}.json`)
*   User secrets configuration
*   Console logging
*   Logging configuration

### What `CreateSlimBuilder` removes (compared to `CreateBuilder`):
*   **Hosting Startup Assemblies** (`IHostingStartup`): Cannot load arbitrary DLLs at runtime with AOT.
*   **Windows EventLog logging provider**: Not registered by default.
*   **Debug logging provider**: Removed.
*   **EventSource logging provider**: Removed (ETW on Windows, LTTng on Linux).
*   **HTTPS endpoints in Kestrel**: Doesn't configure HTTPS by default (can be re-enabled with `builder.WebHost.UseKestrelHttpsConfiguration()`).
*   **HTTP/3 (QUIC)**: Not included by default (can be re-enabled with `builder.WebHost.UseQuic()`). In .NET 10, HTTP/3 code is also **stripped entirely** from AOT/trimmed binaries unless `<Http3Support>true</Http3Support>` is set in the project file.
*   **IIS Integration**: Entirely removed (not just In-Process — IIS integration is not available at all).
*   **UseStaticWebAssets**: Not included (not needed when publishing).
*   **Regex and alpha route constraints**: Removed by default (~1MB size saving). Can be re-enabled:
    ```csharp
    builder.Services.AddRoutingCore().Configure<RouteOptions>(options =>
    {
        options.SetParameterPolicy<RegexInlineRouteConstraint>("regex");
    });
    ```

> [!TIP] CreateEmptyBuilder
> For maximum control and smallest binary size, `WebApplication.CreateEmptyBuilder(new())` is also available. It includes no default configuration at all — you must manually add every service and middleware. This provides the fastest startup but requires the most setup.

## Dependency Injection Constraints

In standard .NET, DI containers often use `Reflection.Emit` to generate efficient service factories.
*   **Default Container**: The built-in `Microsoft.Extensions.DependencyInjection` is AOT-compatible. It falls back to a purely reflection-based (interpreted) mode when Emit is not available. It doesn't crash, but might be slightly slower than JIT-compiled factories.
*   **Third-Party Containers**: Autofac, Lamar, etc., might not work unless they have a specific AOT mode. Check each container's documentation for AOT compatibility.

## Minimal API + JSON

You **must** configure the `JsonSerializerContext` globally for Minimal APIs to work, because the framework cannot use reflection to discover your types at runtime.

```csharp
builder.Services.ConfigureHttpJsonOptions(options =>
{
    options.SerializerOptions.TypeInfoResolverChain.Insert(0, AppJsonContext.Default);
});
```

All types transmitted in HTTP body or returned from Minimal API delegates must be registered on the `JsonSerializerContext`. Query string parameters implementing `IParsable<T>` do not need serialization registration.

## Entity Framework Core

> [!WARNING] EF Core Incompatibility
> Entity Framework Core does **not** support Native AOT publishing. If you need database access in an AOT app, use raw ADO.NET libraries such as `Microsoft.Data.SqlClient`, `Npgsql`, or `MySqlConnector`.

---
See also: [[Serialization]]
