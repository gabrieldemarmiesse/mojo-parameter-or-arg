# Mojo ParameterOrArg

A Mojo library for seamlessly mixing compile-time parameters and runtime arguments, enabling flexible optimization strategies and reducing code duplication.

Some examples and context can be found in the [proposal](https://github.com/gabrieldemarmiesse/mojo/blob/parameter_or_arg/mojo/proposals/parameter-or-arg.md)

## Overview

The `ParameterOrArg` type allows Mojo developers to write functions that can accept both compile-time parameters and runtime arguments seamlessly. This enables:

- **Deferred optimization decisions** - Let callers decide when to optimize
- **Reduced code duplication** - Single function serves both use cases
- **Flexible APIs** - Give users control over performance trade-offs
- **Zero overhead** - No runtime cost when used with compile-time values

## Installation

Add to your project using pixi:

```bash
pixi add mojo-parameter-or-arg
```

## Quick Example

```mojo
import random
from parameter_or_arg import ParameterOrArg, Parameter

def foo(x: ParameterOrArg[T=Int]):
    @parameter
    if x.is_parameter:
        alias y = x.comptime_value
        print("Compile-time value:", y)
    else:
        print("Runtime value:", x.runtime_value())
    print("Value (runtime or compile-time):", x.value())


def main():
    # Runtime usage 
    # (unless inlining and constant folding kick in, then you might get some optimizations)
    foo(5)
    
    # Runtime usage - foo garantees to treat it as a runtime argument"
    var y = Int(random.random_si64(-10, 10))
    foo(y)
    
    # Explicit compile-time usage, foo garantees to treat x as a compile-time parameter
    foo(Parameter[15]())
```

## API Reference

### `ParameterOrArg[T, //, _comptime_value]`

A type that can hold either a compile-time parameter or runtime argument.

**Type Parameters:**
- `T`: The type of the value (must be `Copyable` and `Movable`)
- `_comptime_value`: Internal parameter for compile-time value storage (Optional[T])

**Properties:**
- `is_parameter`: Compile-time alias indicating if this holds a parameter
- `comptime_value`: Compile-time alias to access the parameter value

**Methods:**
- `value() -> T`: Get the value (works in both parameter and runtime contexts)
- `runtime_value() -> T`: Get the runtime value (only valid when `is_parameter` is False)

### `Parameter[T, //, value]`

A marker type to explicitly indicate compile-time parameter usage.

**Type Parameters:**
- `T`: The type of the value (must be `Copyable` and `Movable`)
- `value`: The compile-time value of type T

## Implementation Details

The library leverages Mojo's compile-time parameter system to:
- Store compile-time values as type parameters when possible
- Fall back to runtime storage when needed
- Provide zero-overhead abstraction for compile-time paths


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

Based on the proposal and implementation by Gabriel de Marmiesse for the Mojo standard library.