# Function_Types

A lightweight C++17 header-only library for investigating functions' properties.

## Supported Functions
- Global functions (including Static functions)
- Member functions

## Investigable Properties
### Common Investigable Properties for Global functions and Member functions
- Whether is Member Function
- Return Type
- Parameter Types
- Arity (Number of Parameters)
- Whether is `noexcept`

### Investigable Properties only for Member functions
- Class it's belong to
- Whether is `const`
- Whether is `volatile`
- Whether is l-value reference qualified (i.e. `&`)
- Whether is r-value reference qualified (i.e. `&&`)


## How to Use
`FunctionTypes< [insert function pointer type here] >:: [property you want to investigate]`

### Example
```c++
#include <type_traits>
#include "FunctionTypes.hpp"


class C
{
public:
    [[nodiscard]] C* mem(const int&, int&, int&&, const int(&)[10], int) const && noexcept;
};


int main()
{
    using namespace function_types;

    using MemberFunctionPtrType = decltype(&C::mem);

    using MemberFunctionProperties = FunctionTypes<MemberFunctionPtrType>;
    
    static_assert(MemberFunctionProperties::isMemberFunction);
    static_assert(MemberFunctionProperties::isConst);
    static_assert(!MemberFunctionProperties::isVolatile);
    static_assert(!MemberFunctionProperties::isLRef);
    static_assert(MemberFunctionProperties::isRRef);
    static_assert(MemberFunctionProperties::isNoExcept);
    
    static_assert(std::is_same_v<MemberFunctionProperties::Class, C>);
    static_assert(std::is_same_v<MemberFunctionProperties::ReturnType, C*>);

    using MemberParameters = MemberFunctionProperties::Parameters;
    static_assert(std::tuple_size_v<MemberParameters> == MemberFunctionProperties::parameterCount);
    static_assert(std::is_same_v<std::tuple_element_t<0, MemberParameters>, const int&>);
    static_assert(std::is_same_v<std::tuple_element_t<1, MemberParameters>, int&>);
    static_assert(std::is_same_v<std::tuple_element_t<2, MemberParameters>, int&&>);
    static_assert(std::is_same_v<std::tuple_element_t<3, MemberParameters>, const int(&)[10]>);
    static_assert(std::is_same_v<std::tuple_element_t<4, MemberParameters>, int>);
}
```
