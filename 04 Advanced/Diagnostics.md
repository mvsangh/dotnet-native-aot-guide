# Diagnostics and Observability

Debugging a Native AOT application is closer to debugging C++ than C#.

## Debugging

### Windows
*   **Visual Studio**: You can debug Native AOT apps using the Visual Studio debugger. It feels similar to managed debugging, but some high-level evaluations (like complex LINQ expressions in the Watch window) might not work.
*   **Windbg**: Fully supported for deep analysis.

### Linux/macOS
*   **LLDB / GDB**: You often need to use native debuggers.
*   **`dotnet-dump`**: Works with Native AOT, but the fidelity of the information might be lower than managed apps. You generally cannot "inspect heap" as easily to see all managed objects unless symbols are perfectly preserved.

## Profiling
Standard .NET profilers (like `dotnet-trace`) rely on the EventPipe.
*   **EventPipe** is supported in Native AOT, so tools like `dotnet-counters` and `dotnet-trace` *should* work, but setup might differ.
*   **Native Profilers**: Tools like `perf` (Linux) or Instruments (macOS) see the app as a standard native process, which can sometimes give *better* insight into CPU usage than managed profilers.

## Stack Traces
By default, Native AOT includes data to generate stack traces.
*   If you use `<StripSymbols>true</StripSymbols>`, stack traces will become unreadable (memory addresses instead of method names).
*   **Recommendation**: Keep symbols for production logging if possible, or use symbol mapping files (though tooling for mapping back crash dumps in AOT is less mature than standard .NET).

## EventSource
`EventSource` and `ILogger` generally work, but ensure any custom EventSource classes are not trimmed away.

---
[Return to Start]([[00 Start Here]])
