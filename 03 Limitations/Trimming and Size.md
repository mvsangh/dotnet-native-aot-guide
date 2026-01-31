# Trimming and Size

Trimming is the process of removing unused code from the application. Native AOT requires trimming because it cannot compile *everything* in the .NET framework into a single executable—it would be hundreds of megabytes.

## How Trimming Works
The trimmer starts at your application's **Entry Point** (`Main` method) and follows every static reference (method calls, field access).
1.  It marks code as "used".
2.  It traverses dependencies.
3.  Anything NOT marked is discarded.

## Common Pitfalls

### `Assembly.LoadFile`
Dynamic loading of assemblies is **not supported**. The trimmer cannot see into a DLL that isn't referenced at compile time.
*   **Impact**: Plugin architectures based on dropping DLLs into a folder will not work.
*   **Alternative**: Plugins must be statically referenced and compiled into the app, or you must use a different architecture (e.g., separate processes communicating via gRPC).

### Generics and Value Types
When you use `List<int>`, the compiler generates specific native code for `int`. Each unique **value-type** generic instantiation (e.g., `List<int>`, `List<double>`, `Dictionary<int, long>`) produces its own native code, which can increase binary size. Reference-type generics (e.g., `List<string>`, `List<object>`) share a single code path.

If you use `List<T>` via reflection where `T` is determined at runtime, the compiler might not have generated the necessary native code for that specific `T`, leading to a runtime failure.

## Managing Warnings
You will see warnings like **`IL2026`** or **`IL2091`**. **Do not ignore these.** They indicate a potential runtime crash.

### `IL2026: RequiresUnreferencedCode`
This is the most common AOT warning. It means:
> "The method you are calling is marked as unsafe for trimming because it likely uses reflection to access members that might have been removed."

**Example**: Calling `Type.GetMethod(string name)` often triggers this because the trimmer cannot verify if the method "name" still exists.

### Suppressing Warnings
If you are 100% specific code is safe (e.g., you manually ensured the type is preserved), you can suppress the warning, but this is dangerous.

```csharp
[UnconditionalSuppressMessage("Trimming", "IL2026:RequiresUnreferencedCode", Justification = "I know what I'm doing")]
void MyRiskyMethod() { ... }
```

## Reducing Binary Size
*   **`StripSymbols`**: Removes debugging symbols (see [[Configuration]]).
*   **`InvariantGlobalization`**: If your app acts the same in every culture (mostly server-side), disable ICU (International Components for Unicode) to save ~1MB.
    ```xml
    <InvariantGlobalization>true</InvariantGlobalization>
    ```
*   **`StackTraceSupport`**: If you don't need stack traces in exceptions (e.g., extremely constrained environment).
    ```xml
    <StackTraceSupport>false</StackTraceSupport>
    ```

---
Next: [[Platform and Architecture]]
