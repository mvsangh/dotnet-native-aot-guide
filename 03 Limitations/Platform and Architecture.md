# Platform and Architecture

Unlike standard .NET, which compiles to IL (Intermediate Language) that runs anywhere a runtime exists, Native AOT compiles to **machine code**.

## No Cross-Compilation
> [!ERROR] Critical Constraint
> You cannot build a macOS executable from Windows, or a Linux executable from macOS.

The native toolchain (linker, platform SDKs) is platform-specific.
*   To build for **Linux**, you must be on a Linux machine (or Docker container).
*   To build for **Windows**, you must be on Windows.
*   To build for **macOS**, you must be on a Mac.

### CI/CD Implications
Your CI pipeline must have agents for each target OS.
*   **GitHub Actions**: Use `runs-on: ubuntu-latest`, `windows-latest`, `macos-latest`.
*   **Docker**: You can build Linux binaries inside a Docker container on a Windows host.

## Supported Platforms

| Platform | x64 | ARM64 | Status (.NET 9+) |
| :--- | :--- | :--- | :--- |
| **Windows** | ✅ Supported | ✅ Supported | Stable |
| **Linux** | ✅ Supported | ✅ Supported | Stable |
| **macOS** | ✅ Supported | ✅ Supported | Stable |
| **iOS / tvOS** | — | ✅ Supported | **Stable** (New in .NET 9) |
| **Android** | ✅ Experimental | ✅ Experimental | ⚠️ Experimental |

> [!NOTE] Mobile Support
> As of .NET 9, iOS and Mac Catalyst are officially supported for Native AOT (e.g., in MAUI apps). Android support is active but considered experimental.

## Dependencies
Native AOT apps depend on the underlying OS libraries.
*   **Linux**: Usually depends on `glibc` (standard) or `musl` (Alpine).
*   **Windows**: Depends on Visual C++ Runtime (usually statically linked or available).

If you see "symbol not found" errors at runtime on Linux, it's often a `glibc` version mismatch. Building on an older Linux distro (like Debian 11) increases compatibility with newer ones.

---
Next: [[Diagnostics]]
