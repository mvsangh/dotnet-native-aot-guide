# Reflection and Dynamic Code

> [!DANGER] Core Limitation
> Native AOT **cannot generate new code at runtime**. This fundamental constraint breaks any pattern relying on JIT compilation.

## The "No JIT" Rule
In standard .NET, `System.Reflection.Emit` allows you to create assemblies, types, and methods on the fly. The JIT compiler then compiles this IL to machine code.
**In Native AOT, there is no JIT.** Therefore, `Reflection.Emit` throws a runtime exception.

### The Warning: `IL3050`
The compiler produces warning **`IL3050: RequiresDynamicCode`** when it detects code paths that might trigger dynamic code generation.
> "Members annotated with 'RequiresDynamicCodeAttribute' may break when the AOT compiler is used."

### Broken Scenarios
*   **Mocking Libraries**: Moq, NSubstitute (often rely on dynamic proxies).
*   **Dependency Injection**: Some containers generate dynamic code for service instantiation.
*   **ORMs**: Entity Framework Core (does not support AOT publishing), Dapper (without AOT optimizations).
*   **Scripting**: IronPython, C# Scripting API (`Roslyn`).

## Additional Runtime Limitations

Beyond `Reflection.Emit`, Native AOT has several other limitations that stem from the absence of a JIT:

### No C++/CLI
C++/CLI (managed C++ interop) is not supported in Native AOT. Use P/Invoke or COM interop instead for native code integration.

### No Built-in COM (Windows)
On Windows, built-in COM support is not available in Native AOT. If you need COM interop, you must use manual implementations or alternative approaches.

### `System.Linq.Expressions` — Interpreted Only
`System.Linq.Expressions` works in Native AOT but always uses the **interpreted form** rather than compiling expressions to IL. This means expression trees will execute more slowly than in standard .NET where they can be JIT-compiled. This primarily affects:
*   Expression-based query providers
*   Libraries that compile expression trees for performance
*   Dynamic LINQ scenarios

### Generic Specialization Impact
Native AOT must pre-generate native code for **all struct type instantiations** at compile time (e.g., `List<int>`, `Dictionary<int, double>`). This can impact disk size because each unique value-type generic instantiation produces separate native code. Reference type generics share code.

## Reflection Analysis
Standard reflection (inspecting types) works, *but* the **Trimmer** poses a threat. If you reflect on a type that the trimmer removed (because it didn't see a static reference), the app crashes.

### The Solution: `DynamicallyAccessedMembers`
You must tell the compiler/trimmer which types you intend to reflect upon.

```csharp
void PrintMethods([DynamicallyAccessedMembers(DynamicallyAccessedMemberTypes.PublicMethods)] Type type)
{
    foreach (var method in type.GetMethods())
    {
        Console.WriteLine(method.Name);
    }
}
```

By annotating the `Type` parameter, the trimmer knows: *"If `PrintMethods` is called with `typeof(MyClass)`, keep all public methods of `MyClass`."*

### Unbounded Reflection
Trying to load a type by name from a string is dangerous and often unsupported.

```csharp
// DANGEROUS: Trimmer doesn't know which "MyAssembly.MyType" to keep.
Type.GetType("MyAssembly.MyType");
```

**Workaround**: Use known types or Source Generators to register types explicitly.

## Source Generators: The Hero
The ecosystem is moving to **Source Generators** to solve this. Instead of generating code at *runtime*, they generate code at *compile time*.

*   **Regex**: Use `[GeneratedRegex]` instead of `new Regex("...")`.
*   **Serialization**: Use `System.Text.Json` source gen (see [[Serialization]]).
*   **Logging**: Use `[LoggerMessage]`.
*   **Configuration**: Use source-generated configuration binding.

> [!TIP] Validation Strategy
> Use both Roslyn analyzers (during development) and the AOT compiler (during publish) for validation. Analyzers catch most warnings during coding, but the AOT compiler analyzes the whole program and can find warnings that analyzers miss — especially in third-party libraries that lack trimming annotations.

---
Next: [[Serialization]]
