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
*   **ORMs**: Entity Framework (classic), Dapper (without AOT optimizations).
*   **Scripting**: IronPython, C# Scripting API (`Roslyn`).

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
// ❌ DANGEROUS: Trimmer doesn't know which "MyAssembly.MyType" to keep.
Type.GetType("MyAssembly.MyType");
```

**Workaround**: Use known types or Source Generators to register types explicitly.

## Source Generators: The Hero
The ecosystem is moving to **Source Generators** to solve this. Instead of generating code at *runtime*, they generate code at *compile time*.

*   **Regex**: Use `[GeneratedRegex]` instead of `new Regex("...")`.
*   **Serialization**: Use `System.Text.Json` source gen (see [[Serialization]]).
*   **Logging**: Use `[LoggerMessage]`.

---
Next: [[Serialization]]
