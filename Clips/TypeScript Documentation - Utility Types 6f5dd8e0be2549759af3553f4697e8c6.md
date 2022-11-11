# TypeScript: Documentation - Utility Types

[https://www.typescriptlang.org/docs/handbook/utility-types.html](https://www.typescriptlang.org/docs/handbook/utility-types.html)

- [Decorators](https://www.typescriptlang.org/docs/handbook/decorators.html)[Enums](https://www.typescriptlang.org/docs/handbook/enums.html)[JSX](https://www.typescriptlang.org/docs/handbook/jsx.html)[Mixins](https://www.typescriptlang.org/docs/handbook/mixins.html)[Modules](https://www.typescriptlang.org/docs/handbook/modules.html)[Namespaces](https://www.typescriptlang.org/docs/handbook/namespaces.html)[Symbols](https://www.typescriptlang.org/docs/handbook/symbols.html)

TypeScript provides several utility types to facilitate common type transformations. These utilities are available globally.

> 
> 
> 
> Released: [4.5](https://www.typescriptlang.org/docs/handbook/docs/handbook/release-notes/typescript-4-5.html#the-awaited-type-and-promise-improvements)
> 

This type is meant to model operations like `await` in `async` functions, or the `.then()` method on `Promise`s - specifically, the way that they recursively unwrap `Promise`s.

> 
> 
> 
> Released: [2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#partial-readonly-record-and-pick)
> 

Constructs a type with all properties of `Type` set to optional. This utility will return a type that represents all subsets of a given type.

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#improved-control-over-mapped-type-modifiers)
> 

Constructs a type consisting of all properties of `Type` set to required. The opposite of `[Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)`.

```
Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```

> 
> 
> 
> Released: [2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#partial-readonly-record-and-pick)
> 

Constructs a type with all properties of `Type` set to `readonly`, meaning the properties of the constructed type cannot be reassigned.

This utility is useful for representing assignment expressions that will fail at runtime (i.e. when attempting to reassign properties of a [frozen object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)).

> 
> 
> 
> Released: [2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#partial-readonly-record-and-pick)
> 

Constructs an object type whose property keys are `Keys` and whose property values are `Type`. This utility can be used to map the properties of a type to another type.

> 
> 
> 
> Released: [2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#partial-readonly-record-and-pick)
> 

Constructs a type by picking the set of properties `Keys` (string literal or union of string literals) from `Type`.

> 
> 
> 
> Released: [3.5](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-5.html#the-omit-helper-type)
> 

Constructs a type by picking all properties from `Type` and then removing `Keys` (string literal or union of string literals).

## `Exclude<UnionType, ExcludedMembers>`

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#predefined-conditional-types)
> 

Constructs a type by excluding from `UnionType` all union members that are assignable to `ExcludedMembers`.

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#predefined-conditional-types)
> 

Constructs a type by extracting from `Type` all union members that are assignable to `Union`.

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#predefined-conditional-types)
> 

Constructs a type by excluding `null` and `undefined` from `Type`.

## `Parameters<Type>`

> 
> 
> 
> Released: [3.1](https://github.com/microsoft/TypeScript/pull/26243)
> 

Constructs a tuple type from the types used in the parameters of a function type `Type`.

```
Type 'Function' does not satisfy the constraint '(...args: any) => any'.
  Type 'Function' provides no match for the signature '(...args: any): any'.
```

## `ConstructorParameters<Type>`

> 
> 
> 
> Released: [3.1](https://github.com/microsoft/TypeScript/pull/26243)
> 

Constructs a tuple or array type from the types of a constructor function type. It produces a tuple type with all the parameter types (or the type `never` if `Type` is not a function).

```
Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.
```

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#predefined-conditional-types)
> 

Constructs a type consisting of the return type of function `Type`.

```
Type 'Function' does not satisfy the constraint '(...args: any) => any'.
  Type 'Function' provides no match for the signature '(...args: any): any'.
```

> 
> 
> 
> Released: [2.8](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html#predefined-conditional-types)
> 

Constructs a type consisting of the instance type of a constructor function in `Type`.

```
Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
  Type 'Function' provides no match for the signature 'new (...args: any): any'.
```

## `ThisParameterType<Type>`

> 
> 
> 
> Released: [3.3](https://github.com/microsoft/TypeScript/pull/28920)
> 

Extracts the type of the [this](https://www.typescriptlang.org/docs/handbook/functions.html#this-parameters) parameter for a function type, or [unknown](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type) if the function type has no `this` parameter.

## `OmitThisParameter<Type>`

> 
> 
> 
> Released: [3.3](https://github.com/microsoft/TypeScript/pull/28920)
> 

Removes the `[this](https://www.typescriptlang.org/docs/handbook/functions.html#this-parameters)` parameter from `Type`. If `Type` has no explicitly declared `this` parameter, the result is simply `Type`. Otherwise, a new function type with no `this` parameter is created from `Type`. Generics are erased and only the last overload signature is propagated into the new function type.

> 
> 
> 
> Released: [2.3](https://github.com/microsoft/TypeScript/pull/14141)
> 

This utility does not return a transformed type. Instead, it serves as a marker for a contextual `[this](https://www.typescriptlang.org/docs/handbook/functions.html#this)` type. Note that the `[noImplicitThis](https://www.typescriptlang.org/tsconfig#noImplicitThis)` flag must be enabled to use this utility.

In the example above, the `methods` object in the argument to `makeObject` has a contextual type that includes `ThisType<D & M>` and therefore the type of [this](https://www.typescriptlang.org/docs/handbook/functions.html#this) in methods within the `methods` object is `{ x: number, y: number } & { moveBy(dx: number, dy: number): number }`. Notice how the type of the `methods` property simultaneously is an inference target and a source for the `this` type in methods.

The `ThisType<T>` marker interface is simply an empty interface declared in `lib.d.ts`. Beyond being recognized in the contextual type of an object literal, the interface acts like any empty interface.

## Intrinsic String Manipulation Types

To help with string manipulation around template string literals, TypeScript includes a set of types which can be used in string manipulation within the type system. You can find those in the [Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html#uppercasestringtype) documentation.