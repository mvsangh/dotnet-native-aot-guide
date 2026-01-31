# ASP.NET Core Specifics

Native AOT in ASP.NET Core requires a shift from the "battery-included" defaults to a "pick-what-you-use" approach.

## 🚫 Unsupported Features
The following are **NOT** supported in Native AOT (as of .NET 8):
*   **MVC / Razor Pages**: Anything relying on runtime compilation of views (`.cshtml`) or runtime discovery of Controllers.
*   **Blazor Server**: Not supported.
*   **`startup.cs`**: The `UseStartup<T>` pattern is effectively dead. You must use `Program.cs` and the generic host builder.
*   **IIS Integration**: You cannot host in IIS process (In-Process hosting). You must run as a standalone process (Out-of-Process).

## ✅ Supported Application Models
*   **Minimal APIs**: The primary citizen for AOT.
*   **gRPC**: Fully supported.
*   **Worker Services**: Fully supported.

## The `CreateSlimBuilder`
For AOT, you should use `WebApplication.CreateSlimBuilder(args)` instead of `CreateBuilder(args)`.

### What `CreateSlimBuilder` removes:
*   **Startup Hooks**: No support for hosting startup assemblies.
*   **Windows EventLog**: Not registered by default.
*   **Debug Logger**: Removed.
*   **Kestrel Configuration**: Doesn't load HTTPS certificates from standard configuration sections by default (requires manual config).
*   **Regex Routes**: Route constraints using Regex might need source generator replacements.

## Dependency Injection Constraints
In standard .NET, DI containers often use `Reflection.Emit` to generate efficient service factories.
*   **Default Container**: The built-in `Microsoft.Extensions.DependencyInjection` is AOT-compatible because it falls back to a purely reflection-based (interpreted) mode when Emit is not available. It doesn't crash, but might be slightly slower than JIT-compiled factories.
*   **Third-Party Containers**: Autofac, Lamar, etc., might not work unless they have a specific AOT mode.

## Minimal API + JSON
You **must** configure the `JsonSerializerContext` globally for Minimal APIs to work, because the framework code cannot "see" your types generically.

```csharp
builder.Services.ConfigureHttpJsonOptions(options =>
{
    options.SerializerOptions.TypeInfoResolverChain.Insert(0, AppJsonContext.Default);
});
```

---
See also: [[Serialization]]
