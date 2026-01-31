# What's New in .NET 10 (Native AOT)

.NET 10 marks a significant maturity point for Native AOT, building on the foundations laid in .NET 8 and .NET 9 with improved tooling, broader platform support, and new deployment scenarios.

## Evolution Across Versions

Before diving into .NET 10 specifics, here's how Native AOT has evolved:

| Version | Key AOT Milestones |
| :--- | :--- |
| **.NET 7** | Native AOT introduced for console apps |
| **.NET 8** | ASP.NET Core support (Minimal APIs, gRPC, Worker Services), `IsAotCompatible` property, `CreateSlimBuilder`, `webapiaot` template |
| **.NET 9** | iOS/tvOS/MacCatalyst moved to Stable, SignalR AOT support, Windows x86 and Linux ARM added, expanded analyzer coverage |
| **.NET 10** | Dotnet tools as AOT, HTTP/3 disabled by default for AOT, enhanced metadata enforcement, further size/startup improvements |

## .NET 10 Key Changes

### 1. Dotnet Tools as Native AOT Binaries

.NET 10 adds support for shipping `dotnet tools` as Native AOT binaries. This is a new capability not available in earlier versions.

*   **Benefit**: Near-instant CLI tool startup by eliminating JIT overhead entirely.
*   **Distribution**: NuGet packages can contain platform-specific native binaries for specific RIDs, allowing tools to be distributed as self-contained native executables.
*   **Use Case**: Any `dotnet tool` where startup latency matters (formatters, linters, code generators).

### 2. Enhanced Assembly Metadata & Verification

While `IsAotCompatible` and `VerifyReferenceAotCompatibility` were introduced in .NET 8, **.NET 10 enhances the enforcement mechanism**:

*   Libraries built with `<IsAotCompatible>true</IsAotCompatible>` now embed **standardized assembly metadata** that downstream consumers can programmatically verify.
*   The `<VerifyReferenceAotCompatibility>true</VerifyReferenceAotCompatibility>` property (available since .NET 8) now benefits from broader ecosystem adoption — more NuGet packages ship with the AOT-compatible metadata, making the `IL3058` warnings more actionable.

```xml
<PropertyGroup>
  <!-- Enforce that all referenced assemblies declare AOT compatibility -->
  <VerifyReferenceAotCompatibility>true</VerifyReferenceAotCompatibility>
</PropertyGroup>
```

> [!NOTE] This is NOT a new property
> `IsAotCompatible` and `VerifyReferenceAotCompatibility` have existed since .NET 8. What .NET 10 improves is the **depth of metadata** embedded in assemblies and the percentage of the ecosystem that actually ships with this metadata.

### 3. HTTP/3 Disabled by Default (Breaking Change)

> [!WARNING] .NET 10 Breaking Change
> Starting in .NET 10, HTTP/3 support is **disabled by default** when `PublishAot` or `PublishTrimmed` is set to `true`. The HTTP/3 (QUIC) code is entirely stripped from the binary, saving approximately 800KB.

To re-enable HTTP/3 in an AOT-published app:

```xml
<PropertyGroup>
  <PublishAot>true</PublishAot>
  <Http3Support>true</Http3Support>
</PropertyGroup>
```

This change was introduced because HTTP/3 often doesn't work by default in environments where the `msquic` native library is unavailable, meaning the code was carried but unused in most deployments.

### 4. Performance & Size Improvements

*   **Startup**: Continued improvements to AOT compilation quality have reduced startup times for web APIs.
*   **Size**: Community benchmarks indicate Minimal API apps now produce binaries in the 3-10 MB range (down from 18-25 MB in .NET 8). A simple `HttpClient` app compiles to approximately 4 MB.
*   **Vectorization**: The runtime leverages newer SIMD instruction sets where available for optimized code generation.

### 5. Android Status

While iOS/tvOS/MacCatalyst became stable in .NET 9, **Android** support in .NET 10 remains **Experimental**:
*   Viable for specific high-performance modules via Native Libraries.
*   Full app support (MAUI + AOT) still has friction around Java interoperability (no built-in Java interop bridge).
*   Actively being developed — expect improvements in future releases.

### 6. Broader Ecosystem Compatibility

.NET 10 benefits from the broader .NET ecosystem catching up with AOT:
*   More NuGet packages are annotated with `IsAotCompatible`.
*   Source generators have become the standard pattern for serialization, logging, and configuration.
*   Roslyn analyzers and the AOT compiler produce more accurate and complete warnings.

---
See also: [[Configuration]]
