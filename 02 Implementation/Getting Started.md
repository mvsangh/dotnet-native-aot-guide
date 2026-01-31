# Getting Started with Native AOT

This guide covers the basic steps to enable and publish a Native AOT application.

## Prerequisites
*   **.NET 8 SDK** or later (.NET 10 recommended for the latest AOT improvements).
*   **C++ Compiler** (required because the AOT compiler produces native object files that must be linked):
    *   **Windows**: Visual Studio 2022 with "Desktop development with C++" workload (all default components).
    *   **Linux (Ubuntu 18.04+)**: `sudo apt-get install clang zlib1g-dev`
    *   **Linux (Alpine 3.15+)**: `sudo apk add clang build-base zlib-dev`
    *   **Linux (Fedora 39+)**: `sudo dnf install clang zlib-devel zlib-ng-devel zlib-ng-compat-devel`
    *   **Linux (RHEL 8+)**: `sudo dnf install clang zlib-devel zlib-ng-devel zlib-ng-compat-devel`
    *   **macOS**: Latest Xcode Command Line Tools.

## Project Configuration

To enable Native AOT, add the `<PublishAot>` property to your `.csproj` file. It's preferable to put this in the project file rather than passing it on the command line, since it controls behaviors outside publish (such as enabling AOT analyzers during build and editing).

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <!-- Enable Native AOT -->
    <PublishAot>true</PublishAot>
  </PropertyGroup>

</Project>
```

## Publishing

Native AOT only applies during **publishing**. A normal `dotnet run` or `dotnet build` will still use the standard JIT runtime, which is helpful for the rapid development loop.

To produce the native binary:

```bash
dotnet publish -r <RID> -c Release
```

*   `-r <RID>`: The **Runtime Identifier** is mandatory because native code is platform-specific.
    *   `win-x64`, `win-arm64`
    *   `linux-x64`, `linux-arm64`
    *   `osx-x64`, `osx-arm64`
*   `-c Release`: Always use Release configuration for production builds.

> [!IMPORTANT] OS Version Compatibility
> The native binary produced on Linux only works on the same or newer OS version. For example, a binary built on Ubuntu 20.04 runs on 20.04+ but NOT on 18.04. Building on an older distro increases forward compatibility.

### Example Output
On Linux `linux-x64`, this produces a single executable in `bin/Release/net10.0/linux-x64/publish/`.

```bash
./bin/Release/net10.0/linux-x64/publish/MyApp
```

## "Hello World" Size Comparison

| Method | Size (Approx) |
| :--- | :--- |
| Standard Publish (Self-Contained) | ~65 MB |
| Native AOT (Default) | ~13 MB |
| Native AOT (Stripped, InvariantGlobalization) | ~3 MB |

> [!NOTE]
> Actual sizes vary by application complexity, target framework version, and optimization settings. Community benchmarks and Microsoft blog posts indicate .NET 10 has further reduced baseline sizes for Minimal API apps (typically 3-10 MB, down from 18-25 MB in .NET 8).

---
Next: [[Configuration]] | See also: [[Trimming and Size]]
