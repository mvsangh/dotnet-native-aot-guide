# Platform and Architecture

Unlike standard .NET, which compiles to IL (Intermediate Language) that runs anywhere a runtime exists, Native AOT compiles to **machine code** for a specific platform and architecture.

## Cross-Compilation

### Cross-OS: NOT Supported
> [!ERROR] Critical Constraint
> You cannot build a macOS executable from Windows, or a Linux executable from macOS. Cross-OS compilation is not officially supported.

The native toolchain (linker, platform SDKs) is platform-specific. The build machine's OS must match the target OS.
*   To build for **Linux**, you must be on a Linux machine (or Docker container).
*   To build for **Windows**, you must be on Windows.
*   To build for **macOS**, you must be on a Mac.

> [!NOTE]
> Cross-OS compilation (e.g., Windows to Linux, macOS to Windows) is not possible with the official toolchain because there is no standardized way to obtain native SDKs across platforms. Unofficial community tools exist but are not supported by Microsoft.

### Cross-Architecture: Supported (Same OS)
Native AOT **does** support cross-architecture compilation between **x64 and ARM64** on the same OS:

*   **Windows**: Install the appropriate VS 2022 C++ build tools for the target architecture ("VS 2022 C++ ARM64/ARM64EC build tools" for ARM64, or "VS 2022 C++ x64/x86 build tools" for x64).
*   **macOS**: Both x64 and arm64 toolchains are included in the default Xcode installation.
*   **Linux**: Requires manual installation of cross-architecture toolchain dependencies (e.g., `binutils-aarch64-linux-gnu`, `gcc-aarch64-linux-gnu`, `zlib1g-dev:arm64` on Ubuntu for amd64-to-arm64 builds).

### CI/CD Implications
Your CI pipeline must have agents for each target OS.
*   **GitHub Actions**: Use `runs-on: ubuntu-latest`, `windows-latest`, `macos-latest`.
*   **Docker**: You can build Linux binaries inside a Docker container on a Windows or macOS host (since the container runs Linux).
*   **Cross-arch in CI**: On a single OS agent, you can build for multiple architectures if the cross-compilation toolchain is installed.

## Supported Platforms

### .NET 8

| Platform | x64 | ARM64 | Notes |
| :--- | :--- | :--- | :--- |
| **Windows** | Supported | Supported | Stable |
| **Linux** | Supported | Supported | Stable |
| **macOS** | Supported | Supported | Stable |
| **iOS** | --- | Supported | Experimental |
| **iOSSimulator** | Supported | Supported | Experimental |
| **tvOS** | --- | Supported | Experimental |
| **tvOSSimulator** | Supported | Supported | Experimental |
| **MacCatalyst** | Supported | Supported | Experimental |
| **Android** | Supported | Supported | Experimental, no built-in Java interop |

### .NET 9+ (including .NET 10)

| Platform | x86 | x64 | ARM | ARM64 | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Windows** | Supported | Supported | --- | Supported | Stable |
| **Linux** | --- | Supported | Supported | Supported | Stable |
| **macOS** | --- | Supported | --- | Supported | Stable |
| **iOS** | --- | --- | --- | Supported | **Stable** (since .NET 9) |
| **iOSSimulator** | --- | Supported | --- | Supported | **Stable** (since .NET 9) |
| **tvOS** | --- | --- | --- | Supported | **Stable** (since .NET 9) |
| **tvOSSimulator** | --- | Supported | --- | Supported | **Stable** (since .NET 9) |
| **MacCatalyst** | --- | Supported | --- | Supported | **Stable** (since .NET 9) |
| **Android** | --- | Supported | Supported | Supported | Experimental, no built-in Java interop |

> [!NOTE] Key changes in .NET 9 and .NET 10
> *   Windows gained **x86** support (.NET 9).
> *   Linux gained **ARM (32-bit)** support (.NET 9).
> *   iOS, tvOS, MacCatalyst, and their simulators moved from **Experimental** to **Stable** (.NET 9).
> *   Android gained **ARM (32-bit)** support but remains **Experimental** through .NET 10.
> *   .NET 10 continues with the same platform matrix as .NET 9, with no new platform additions but improved stability and tooling across all supported targets.

## Dependencies

Native AOT apps depend on the underlying OS libraries.
*   **Linux**: Usually depends on `glibc` (standard distributions) or `musl` (Alpine). Use `linux-musl-x64` RID for Alpine/musl-based containers.
*   **Windows**: Depends on Visual C++ Runtime (usually statically linked or already available on the system).
*   **macOS**: Depends on system frameworks provided by macOS.

> [!WARNING] glibc Version Mismatch
> If you see "symbol not found" errors at runtime on Linux, it's often a `glibc` version mismatch. The binary requires the same or newer `glibc` than what was on the build machine. Building on an older Linux distro (like Debian 11 or Ubuntu 20.04) increases forward compatibility with newer distributions.

---
Next: [[Diagnostics]]
