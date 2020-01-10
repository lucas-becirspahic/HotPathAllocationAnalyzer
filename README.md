Roslyn Clr Heap Allocation Analyzer
===================================

Roslyn Analyzer detecting heap allocation in *hot path* (based on https://github.com/microsoft/RoslynClrHeapAllocationAnalyzer) 

Detect in hot path:  
 - explicit allocation 
 - implicit allocations 
    - boxing 
    - display classes a.k.a closures 
    - implicit delegate creations
    - ...

## Hot path

Hot path should be flagged as so using the `RestrictedAllocation` attribute. This attribute indicate that the analyzer should run on a method.

It also forbid calling a method/property that is considered unsafe. 

Methods/Properties are considered safe when flagged for analysis (`RestrictedAllocation`), flagged for ignore (`RestrictedAllocationIgnore`) or whitelisted.
Properties are also considered safe when they are auto property. 

## Setup

Install the nuget package on the project to analyze

## Whitelisting

Whitelisting can be used to mark third party and system methods as safe.

1. To create a whitelist, you need to create a folder called `ClrHeapAllocationAnalyzer` at the root of your solution. 
2. This folder should contain a `csproj` (name does not matter) and reference the nuget `ClrHeapAllocationAnalyzer.Configuration`
3. You can then add some `class` in the project that implement the `AllocationConfiguration` class and define some method listing whitelisted methods:

```cs
public class TestConfiguration : AllocationConfiguration
{
    public void WhitelistString(string str)
    {
        MakeSafe(() => str.Length);
        MakeSafe(() => str.IsNormalized());
        MakeSafe(() => str.Contains(default));
    }

    public void WhitelistNullable<T>(T? arg)
        where T: struct
    {
        MakeSafe(() => arg.Value); 
        MakeSafe(() => arg.HasValue); 
    }
}
```  