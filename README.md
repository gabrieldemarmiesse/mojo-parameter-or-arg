# Mojo ParameterOrArg

A Mojo library for seamlessly mixing compile-time parameters and runtime arguments, enabling flexible optimization strategies and reducing code duplication.

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
from parameter_or_arg import ParameterOrArg, Parameter

def maybe_parameter(x: ParameterOrArg[T=Int]):
    @parameter
    if x.value() < 10:
        print("x is less than 10")
    else:
        print("x is greater than or equal to 10")

def main():
    # Compile-time usage - condition is evaluated at compile time
    maybe_parameter(5)
    
    # Runtime usage - condition is evaluated at runtime
    var y = 20
    maybe_parameter(y)
    
    # Explicit compile-time usage
    maybe_parameter(Parameter[15]())
```

## Key Features

### 1. Flexible Function Arguments

Write functions that work with both compile-time and runtime values:

```mojo
def flexible_buffer_size(size: ParameterOrArg[T=Int]) -> String:
    @parameter
    if size.value() == 1024:
        return "Optimized for 1KB buffer"
    else:
        return "Generic buffer handling"
```

### 2. Struct Design Flexibility

Create structs that can be either statically or dynamically sized:

```mojo
struct FlexibleBuffer[_SizeParam: Optional[Int], //]:
    var data: UnsafePointer[UInt8]
    var size: ParameterOrArg[_SizeParam]
    
    fn __init__(out self, size: ParameterOrArg[_SizeParam]):
        self.size = size
        self.data = UnsafePointer[UInt8].alloc(self.size.value())
```

### 3. Performance Control

Give users explicit control over optimization:

```mojo
# Let the library optimize when possible
process_data(1024)  # May be optimized

# Force compile-time optimization
process_data(Parameter[1024]())  # Guaranteed optimization

# Handle runtime values
var size = get_user_input()
process_data(size)  # Runtime handling
```

## Use Cases

1. **Matrix operations** - Allow both fixed-size and dynamic matrices
2. **Buffer management** - Support both static and dynamic buffer sizes
3. **Algorithm selection** - Choose implementations based on compile-time or runtime parameters
4. **Template specialization** - Provide optimized paths for common cases

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