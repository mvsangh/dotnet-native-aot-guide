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
By default, Native AOT includes data to generate readable stack traces via the `<StackTraceSupport>true</StackTraceSupport>` property (enabled by default).
*   If you set `<StripSymbols>true</StripSymbols>`, the **separate** symbol files (`.pdb`, `.dbg`, `.dSYM`) are not generated — but the binary itself still contains stack trace metadata unless `StackTraceSupport` is also disabled.
*   If you disable `<StackTraceSupport>false</StackTraceSupport>`, stack traces in exceptions will show memory addresses instead of method names.
*   **Recommendation**: Keep both defaults (`StackTraceSupport=true`, separate symbol files generated) for production. Only disable for extremely size-constrained environments.

## EventSource
`EventSource` and `ILogger` generally work, but ensure any custom EventSource classes are not trimmed away.

---
[Return to Start]([[00 Start Here]])
