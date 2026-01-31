# Introduction to Native AOT

**Native AOT** (Ahead-of-Time) compilation produces a self-contained application that has been compiled to native code (machine code) at build time, rather than compiled to IL (Intermediate Language) and JIT (Just-In-Time) compiled at runtime. Native AOT was introduced in .NET 7 for console apps and expanded to ASP.NET Core in .NET 8. As of .NET 10, it is a mature deployment option for many workload types.

## Key Benefits

### 🚀 Startup Time
The application starts running code immediately. There is no JIT compilation pause, and no need to load the JIT compiler itself. This is ideal for:
*   **Serverless/Lambda functions** (avoids "cold start").
*   **CLI tools** (instant response).
*   **Kubernetes pods** (faster scaling).

### 💾 Memory Footprint
AOT apps use less memory because:
*   No JIT compiler is loaded into memory.
*   No intermediate IL code is stored.
*   Aggressive **trimming** removes unused code.

### 📦 Self-Contained
The output is a single binary (mostly) that does not require a pre-installed .NET Runtime on the target machine.

## Trade-offs

| Feature | JIT (Standard .NET) | Native AOT |
| :--- | :--- | :--- |
| **Throughput** | High (Tiered Compilation optimizes hot paths) | Good (PGO helps, but static) |
| **Disk Size** | Small (relies on shared runtime) | Larger (includes runtime) *but* trimmable |
| **Dynamic Features** | Full support (Reflection.Emit, dynamic loading) | **Not Supported** |
| **Cross-Platform** | "Write once, run anywhere" (IL) | **Build per platform** |
| **Diagnostics** | Full debugger and profiler support | Limited (native-level debugging) |
| **Ecosystem** | All NuGet packages work | Only AOT-compatible packages |

## When to use Native AOT?
> [!TIP] Recommendation
> Use Native AOT when **startup time** and **deployment size** are critical, and you can accept the **development complexity** of avoiding dynamic features.

For long-running high-throughput servers, standard JIT might still offer better peak performance due to runtime adaptability.

---
Next: [[Getting Started]]
