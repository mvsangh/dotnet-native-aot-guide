# References

The following resources were consulted to generate and verify this documentation vault.

## Official Microsoft Documentation
*   [Native AOT deployment overview](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/) - The foundational guide for .NET Native AOT (updated Jan 2026).
*   [ASP.NET Core support for Native AOT](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/native-aot?view=aspnetcore-10.0) - Specific limitations for Web APIs and hosting (.NET 10 view).
*   [Tutorial: Publish an ASP.NET Core app using Native AOT](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/aot/native-aot-tutorial) - Step-by-step tutorial for the `webapiaot` template.
*   [Cross-compilation - .NET Native AOT](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/cross-compile) - Cross-architecture compilation support and limitations.
*   [Breaking change: HTTP/3 disabled by default with PublishTrimmed (.NET 10)](https://learn.microsoft.com/en-us/dotnet/core/compatibility/networking/10.0/http3-disabled-with-publishtrimmed) - .NET 10 breaking change for AOT/trimmed apps.
*   [Breaking changes in .NET 10](https://learn.microsoft.com/en-us/dotnet/core/compatibility/10) - Full list of .NET 10 breaking changes.
*   [Introduction to AOT warnings](https://learn.microsoft.com/en-us/dotnet/core/deploying/trimming/trim-warnings) - Source for trimming warning codes (IL2026, IL3050, IL3058, etc.).
*   [IL2026 Warning](https://learn.microsoft.com/en-us/dotnet/core/deploying/trimming/trim-warnings/il2026) - RequiresUnreferencedCode warning specifics.
*   [System.Text.Json source generation](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/source-generation) - JSON serializer source generator documentation.
*   [Optimizing AOT deployments](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/optimizing) - MSBuild properties and optimization strategies.
*   [How to make libraries compatible with Native AOT](https://devblogs.microsoft.com/dotnet/creating-aot-compatible-libraries/) - Official guide for library authors.
*   [Use hubs in ASP.NET Core SignalR](https://learn.microsoft.com/en-us/aspnet/core/signalr/hubs?view=aspnetcore-10.0) - SignalR hub usage including AOT limitations.

## .NET Version Release Notes
*   [Announcing .NET 8](https://devblogs.microsoft.com/dotnet/announcing-dotnet-8/) - .NET 8 release with initial ASP.NET Core AOT support.
*   [What's new in ASP.NET Core 9.0](https://learn.microsoft.com/en-us/aspnet/core/release-notes/aspnetcore-9.0) - .NET 9 release including SignalR AOT support and mobile platform stabilization.

## Community Resources & Analysis
*   [NDepend Blog: .NET Native AOT Explained](https://blog.ndepend.com/net-native-aot-explained/) - Comprehensive technical analysis.
*   [Stackademic: Surviving Native AOT - The Reflection Migration Guide](https://blog.stackademic.com/surviving-native-aot-the-reflection-migration-guide-every-net-architect-needs-fa3760fbb41b) - Migration patterns and ecosystem shift analysis.
*   [Andrew Lock: Comparing CreateBuilder to CreateSlimBuilder](https://andrewlock.net/exploring-the-dotnet-8-preview-comparing-createbuilder-to-the-new-createslimbuilder-method/) - In-depth code comparison of builder methods.
*   [Andrew Lock: Packaging Native AOT .NET tools for NuGet (.NET 10)](https://andrewlock.net/exploring-dotnet-10-preview-features-7-packaging-self-contained-and-native-aot-dotnet-tools-for-nuget/) - .NET 10 tools as AOT binaries.
*   [ABP.IO: .NET 9.0 SignalR supports trimming and Native AOT](https://abp.io/community/articles/.net-9.0-signalr-supports-trimming-and-native-aot-4oxx0qbs) - SignalR AOT support details.
*   [Sentry Engineering: Native AOT in .NET](https://sentry.engineering/blog/native-aot-in-dotnet) - Real-world AOT adoption experience.
*   [TypedSignalR.Client](https://github.com/nenoNaninu/TypedSignalR.Client) - Source-generator-based strongly typed SignalR client (AOT-compatible).

## GitHub
*   [dotnet/runtime: Trimming and Native AOT in .NET 9 Epic](https://github.com/dotnet/runtime/issues/96511) - Tracking issue for .NET 9 AOT improvements.
*   [dotnet/runtimelab: NativeAOT branch](https://github.com/dotnet/runtimelab/tree/feature/NativeAOT) - Experimental fork of CoreCLR for Native AOT.
*   [dotnet/runtime Discussions: Native AOT Roadmap](https://github.com/dotnet/runtime/discussions) - Community discussions on AOT roadmap.
*   [Native AOT Samples](https://github.com/dotnet/samples/tree/main/core/nativeaot) - Official sample projects including Dockerfiles.

## Cloud Provider Guides
*   [AWS Lambda: Compile .NET Lambda function code to native runtime](https://docs.aws.amazon.com/lambda/latest/dg/dotnet-native-aot.html) - AWS Lambda Native AOT deployment.

---
[Return to Start]([[00 Start Here]])
