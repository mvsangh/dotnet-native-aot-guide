# Serialization

Serialization has historically relied heavily on reflection to inspect properties and instantiate objects. This makes traditional serializers (like `Newtonsoft.Json`) largely incompatible or very difficult to configure for Native AOT.

## The Problem with `Newtonsoft.Json`
`Newtonsoft.Json` makes extensive use of:
1.  **Reflection**: To discover properties.
2.  **`Reflection.Emit`**: To generate fast accessors at runtime.
3.  **Dynamic Typing**: `JObject`, `dynamic`.

While it can run in a "reflection-only" mode, it often requires manual `rd.xml` configuration to prevent the trimmer from stripping your data classes, which is brittle and error-prone.

## The Solution: `System.Text.Json` Source Generator
.NET 6+ introduced a source generator for `System.Text.Json`. It generates C# code at build time to handle serialization, removing the need for runtime reflection.

### How to Implement

1.  Create a partial `JsonSerializerContext`.
2.  Annotate it with `[JsonSerializable]`.

```csharp
using System.Text.Json.Serialization;

public record WeatherForecast(DateOnly Date, int TemperatureC, string? Summary);

[JsonSerializable(typeof(WeatherForecast))]
internal partial class SourceGenerationContext : JsonSerializerContext
{
}
```

3.  Pass the context to the serializer.

```csharp
var weather = new WeatherForecast(DateOnly.FromDateTime(DateTime.Now), 25, "Sunny");

// Serialize
string json = JsonSerializer.Serialize(weather, SourceGenerationContext.Default.WeatherForecast);

// Deserialize
var parsed = JsonSerializer.Deserialize(json, SourceGenerationContext.Default.WeatherForecast);
```

## Limitations of Source Generation
Even with the generator, some patterns are unsupported:
*   **Anonymous Types**: You cannot put `[JsonSerializable]` on an anonymous type.
*   **`dynamic`**: Not supported.
*   **Missing Constructors**: The target type must have a public parameterless constructor or be a record with a supported parameterized constructor.

> [!TIP]
> Always enable `IsAotCompatible` in your project to get warnings if you accidentally use a reflection-based overload of `JsonSerializer.Serialize`.

---
Next: [[Trimming and Size]]
