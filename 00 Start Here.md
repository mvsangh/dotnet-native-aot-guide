# .NET Native AOT Guide

Welcome to the comprehensive guide for **.NET Native AOT**. This vault covers implementation details, configuration, and a deep dive into limitations.

## 📂 Navigation

### 01 Overview
* [[Introduction to Native AOT]] - What is it, benefits, and trade-offs.

### 02 Implementation
* [[Getting Started]] - How to enable AOT and publish your first app.
* [[Configuration]] - Project settings, RID targeting, and dependency management.

### 03 Limitations (Deep Dive)
> [!WARNING] Critical Section
> Native AOT fundamentally changes how .NET code is executed. Understanding these limitations is mandatory for successful adoption.

* [[Reflection and Dynamic Code]] - Why `System.Reflection.Emit` fails and how to handle reflection.
* [[Serialization]] - Moving from `Newtonsoft.Json` to Source Generators.
* [[ASP-NET Core Specifics]] - Minimal APIs, CreateSlimBuilder, and features to avoid.
* [[Trimming and Size]] - Understanding the trimmer, warnings, and keeping binary size low.
* [[Platform and Architecture]] - OS constraints and cross-compilation rules.

### 04 Advanced
* [[NET 10 New Features]] - Version history (.NET 7-10), dotnet tools as AOT, and .NET 10 improvements.
* [[Diagnostics]] - Debugging, profiling, and observability in a native environment.

### Appendix
* [[99 References]] - List of sources and links used to generate this guide.

---
**Tags**: #dotnet #aot #native #performance #compilation
