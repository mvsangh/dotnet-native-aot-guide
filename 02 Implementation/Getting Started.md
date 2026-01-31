# Getting Started with Native AOT

This guide covers the basic steps to enable and publish a Native AOT application.

## Prerequisites
*   **.NET 8 SDK** or later (recommended).
*   **C++ Compiler**:
    *   **Windows**: Visual Studio 2022 with "Desktop development with C++" workload.
    *   **Linux**: `clang` and `zlib1g-dev` (Ubuntu: `sudo apt-get install clang zlib1g-dev`).
    *   **macOS**: Xcode Command Line Tools.

## Project Configuration

To enable Native AOT, add the `<PublishAot>` property to your `.csproj` file.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <!-- Enable Native AOT -->
    <PublishAot>true</PublishAot>
  </PropertyGroup>

</Project>
```

## Publishing

Native AOT only applies during **publishing**. A normal `dotnet run` or `dotnet build` will still use the standard runtime (helpful for rapid development loop).

To produce the native binary:

```bash
dotnet publish -r <RID> -c Release
```

*   `-r <RID>`: The **Runtime Identifier** is mandatory because native code is platform-specific.
    *   `win-x64`
    *   `linux-x64`
    *   `osx-x64` (or `osx-arm64`)
*   `-c Release`: Always use Release configuration for production builds.

### Example Output
On Linux `linux-x64`, this produces a single executable in `bin/Release/net8.0/linux-x64/publish/`.

```bash
./bin/Release/net8.0/linux-x64/publish/MyApp
```

## "Hello World" Size Comparison

| Method | Size (Approx) |
| :--- | :--- |
| Standard Publish (Self-Contained) | ~65 MB |
| Native AOT (Default) | ~13 MB |
| Native AOT (No Symbols, Trimmed) | ~3 MB |

---
Next: [[Configuration]] | See also: [[Trimming and Size]]
