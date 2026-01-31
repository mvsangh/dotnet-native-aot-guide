# What's New in .NET 10 (Native AOT)

.NET 10 marks a significant maturity point for Native AOT, shifting many scenarios from "experimental" to "production-ready" and introducing strict compatibility mechanisms.

## Key Changes

### 1. `IsAotCompatible` Assembly Metadata
In previous versions, AOT compatibility was often a guessing game or relied on runtime testing. .NET 10 introduces a standardized assembly metadata attribute that libraries can publish.

*   **Mechanism**: Libraries built with `<IsAotCompatible>true</IsAotCompatible>` now embed specific metadata in the DLL.
*   **Verification**: You can enforce that *all* your dependencies are explicitly marked as compatible.

```xml
<PropertyGroup>
  <!-- New in .NET 10: Warn if any referenced DLL lacks the AOT-compatible metadata -->
  <VerifyReferenceAotCompatibility>true</VerifyReferenceAotCompatibility>
</PropertyGroup>
```
*   **Warning**: This produces **`IL3058`** if you reference a library that hasn't been officially updated for AOT, saving you from runtime surprises.

### 2. Dotnet Tools Support
You can now ship `dotnet tools` as Native AOT binaries.
*   **Benefit**: Instant command-line tool startup (0ms vs 200ms+ JIT overhead).
*   **Distribution**: NuGet packages can contain the native binary for specific RIDs.

### 3. Performance & Size
*   **Startup**: Web APIs now consistently start in the **5-40ms** range.
*   **Size**: Minimal APIs have been further shrunk (3-10 MB typical).
*   **AVX10.2**: The runtime now utilizes AVX10.2 instructions where available for optimized vectorization.

### 4. Android Status
While iOS/tvOS became stable in .NET 9, **Android** support in .NET 10 is still technically "Experimental" but highly active. It is viable for specific high-performance modules (via Native Libraries) but full app support (MAUI + AOT) still has friction points around Java interoperability.

---
See also: [[Configuration]]
