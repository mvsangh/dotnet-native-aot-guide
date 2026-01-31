# Configuration

Fine-tune your Native AOT build with these MSBuild properties.

## Strip Symbols
To significantly reduce the binary size on Linux/macOS, you can strip debugging symbols. This makes stack traces less readable but saves space.

```xml
<PropertyGroup>
  <StripSymbols>true</StripSymbols>
</PropertyGroup>
```

> [!NOTE] Windows
> On Windows, debugging information is stored in a separate `.pdb` file by default, so `StripSymbols` has less impact on the main executable size.

## Library Compatibility
If you are building a library that is intended to be consumed by AOT apps, mark it as AOT-compatible to get compile-time warnings.

```xml
<PropertyGroup>
  <IsAotCompatible>true</IsAotCompatible>
</PropertyGroup>
```

### Strict Verification (.NET 10+)
To ensure you aren't accidentally using non-compatible libraries, enable strict verification.

```xml
<PropertyGroup>
  <VerifyReferenceAotCompatibility>true</VerifyReferenceAotCompatibility>
</PropertyGroup>
```
*   **Effect**: Raises warning `IL3058` for any dependency lacking the AOT compatibility metadata.

## Optimization Preferences
You can trade compilation speed for code quality/size.

```xml
<PropertyGroup>
  <!-- Speed up the build, but code might run slightly slower/larger -->
  <OptimizationPreference>Speed</OptimizationPreference> 
  <!-- OR -->
  <!-- Default: Maximum optimization (slower build) -->
  <OptimizationPreference>Size</OptimizationPreference>
</PropertyGroup>
```

## Static Linking (Linux)
By default, Native AOT links dynamically against `libc`. You can attempt static linking for a truly portable binary (Distroless), specifically using `musl` libc (e.g., Alpine Linux).

```bash
# Requires Alpine or a musl-based environment
dotnet publish -r linux-musl-x64
```

---
Next: [[Reflection and Dynamic Code]]
