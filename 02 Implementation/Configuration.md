# Configuration

Fine-tune your Native AOT build with these MSBuild properties.

## Strip Symbols

On all platforms, the AOT compiler produces debug information in **separate files** by default (`.pdb` on Windows, `.dbg` on Linux, `.dSYM` on macOS). The `StripSymbols` property controls whether these separate symbol files are generated.

```xml
<PropertyGroup>
  <!-- true = do not generate separate symbol files (smaller deployment, unreadable stack traces) -->
  <StripSymbols>true</StripSymbols>
</PropertyGroup>
```

To embed debug info directly **into** the native binary (larger file, but self-contained debugging):

```xml
<PropertyGroup>
  <!-- false = include debug info in the native binary itself -->
  <StripSymbols>false</StripSymbols>
</PropertyGroup>
```

> [!NOTE] Default Behavior
> When `StripSymbols` is not set, the compiler generates separate symbol files alongside the binary. This is the recommended default for most scenarios.

## Library Compatibility

If you are building a library that is intended to be consumed by AOT apps, mark it as AOT-compatible. This property was introduced in **.NET 8** and automatically enables several analyzers.

```xml
<PropertyGroup>
  <IsAotCompatible>true</IsAotCompatible>
</PropertyGroup>
```

Setting `IsAotCompatible=true` automatically enables:
*   `IsTrimmable=true`
*   `EnableTrimAnalyzer=true`
*   `EnableSingleFileAnalyzer=true`
*   `EnableAotAnalyzer=true`

> [!NOTE] Multi-Targeting
> If your library targets multiple frameworks, conditionally enable AOT compatibility:
> ```xml
> <PropertyGroup>
>   <TargetFrameworks>netstandard2.0;net8.0;net10.0</TargetFrameworks>
>   <IsAotCompatible Condition="$([MSBuild]::IsTargetFrameworkCompatible('$(TargetFramework)', 'net8.0'))">true</IsAotCompatible>
> </PropertyGroup>
> ```

### Strict Verification

To ensure you aren't accidentally using non-compatible libraries, enable strict verification. This is available in .NET 8+ and was further improved in .NET 10 with enhanced assembly metadata.

```xml
<PropertyGroup>
  <IsAotCompatible>true</IsAotCompatible>
  <VerifyReferenceAotCompatibility>true</VerifyReferenceAotCompatibility>
</PropertyGroup>
```
*   **Effect**: Raises warning `IL3058` for any dependency lacking the AOT compatibility metadata.

## Optimization Preferences

You can trade compilation speed for code quality/size.

```xml
<PropertyGroup>
  <!-- Optimize for speed: faster generated code, potentially larger binary -->
  <OptimizationPreference>Speed</OptimizationPreference>
  <!-- OR -->
  <!-- Optimize for size: smaller binary, potentially slower generated code -->
  <OptimizationPreference>Size</OptimizationPreference>
</PropertyGroup>
```

## All Key MSBuild Properties

| Property | Default | Purpose |
| :--- | :--- | :--- |
| `PublishAot` | `false` | Enable Native AOT compilation at publish time |
| `IsAotCompatible` | `false` | Mark a library as AOT-compatible (enables analyzers) |
| `IsTrimmable` | `false` | Mark a library as safe for trimming |
| `EnableAotAnalyzer` | `false` | Enable the AOT compatibility Roslyn analyzer |
| `VerifyReferenceAotCompatibility` | `false` | Warn on references lacking AOT metadata |
| `StripSymbols` | (platform default) | Control debug symbol generation |
| `OptimizationPreference` | (balanced) | `Speed` or `Size` optimization trade-off |
| `InvariantGlobalization` | `false` | Disable ICU for culture-invariant apps (~1MB saving) |
| `StackTraceSupport` | `true` | Include stack trace metadata in the binary |

## Static Linking (Linux)

By default, Native AOT links dynamically against `libc`. You can produce a statically-linked binary using `musl` libc (e.g., Alpine Linux) for truly portable binaries.

```bash
# Requires Alpine or a musl-based environment
dotnet publish -r linux-musl-x64
```

This produces a binary with no dynamic library dependencies, suitable for distroless or scratch containers.

---
Next: [[Reflection and Dynamic Code]]
