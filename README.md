# Symbol.operator

This proposal introduces the `Symbol.operator` and `Symbol.unaryOperator` functions to the ECMAScript Standard Library. The `Symbol.operator` and `Symbol.unaryOperator` functions can be used to extend prototypes to overload operator behaviours, with a future scope of being able to easily add new operators.

`Symbol.operator` and `Symbol.unaryOperator` behave a lot like `Symbol.for` in that they search for existing symbols in a runtime-wide symbol registry with the given key and returns it if found. However, these registries exist separately from `Symbol.for` - so calling `Symbol.for('+')` will return a different symbol to `Symbol.operator('+')` which will return a different symbol to `Symbol.unaryOperator('+')`.

Where an operator is used with two operands _of the same type_, a lookup begins for `<rho>.__proto__.constructor[Symbol.operator(<operator>)]` (for example, the `+` operator will look up `<rho>.__proto__.constructor[Symbol.operator('+')]`). If the property exists and is a function, then the function is called, with the left hand operand as the first argument and the right hand operand as its second argument. The function's return value is what will be used as the result for the operarion.

Where an operator is used with one operand, for example the `!` operator, the single operand will be evaluated to check the existance of the `<operand>.__proto__.constructor[Symbol.unaryOperator(<operator>)]` property (for example the `!` operator will look up `<operand>.__proto__.constructor[Symbol.unaryOperator(`!`)]`). If the property exists and is a function, then the function is called with the operand as the first and only argument. The function's return value will be used as the result of the operation.

Operators may additionally coerce return values for comformity across the platform. For example the `!` operator will coerce any return value `ToBoolean`. As another exampe, the unary `+` operator will always coerce `ToNumber` to maintain compatibility with existing expecation in areas like asm.js.

## Supported Operators

The proposed supported operators are:

 - [Additive](https://www.ecma-international.org/ecma-262/7.0/#sec-applying-the-additive-operators-to-numbers): `+` (`Symbol.operator('+')`)
 - [Subtraction](https://www.ecma-international.org/ecma-262/7.0/#sec-subtraction-operator-minus): `-` (`Symbol.operator('-')`)
 - [Multiplicative / (division)](https://www.ecma-international.org/ecma-262/7.0/#sec-applying-the-div-operator): `/` (`Symbol.operator('/')`)
 - [Multiplicative * (multiplication)](https://www.ecma-international.org/ecma-262/7.0/#sec-applying-the-mul-operator): `*` (`Symbol.operator('*')`)
 - [Multiplicative % (mod)](https://www.ecma-international.org/ecma-262/7.0/#sec-applying-the-mod-operator): `%` (`Symbol.operator('%')`)
 - [Exponentiation](https://www.ecma-international.org/ecma-262/7.0/#sec-applying-the-exp-operator): `**` (`Symbol.operator('**')`)
 
 - [BitWise Left Shift](https://www.ecma-international.org/ecma-262/7.0/#sec-left-shift-operator): `<<` (`Symbol.operator('<<')`)
 - [BitWise Signed Right Shift](https://www.ecma-international.org/ecma-262/7.0/#sec-signed-right-shift-operator): `>>` (`Symbol.operator('>>')`)
 - [BitWise Unsigned Right Shift](https://www.ecma-international.org/ecma-262/7.0/#sec-unsigned-right-shift-operator): `>>>` (`Symbol.operator('>>>')`)
 - [BitWise AND](https://www.ecma-international.org/ecma-262/7.0/#sec-binary-bitwise-operators-runtime-semantics-evaluation): `&` (`Symbol.unaryOperator('&')`)
 - [BitWise OR](https://www.ecma-international.org/ecma-262/7.0/#sec-binary-bitwise-operators-runtime-semantics-evaluation): `|` (`Symbol.unaryOperator('|')`)
 - [BitWise XOR](https://www.ecma-international.org/ecma-262/7.0/#sec-binary-bitwise-operators-runtime-semantics-evaluation): `~` (`Symbol.unaryOperator('^')`)
 - [BitWise NOT](https://www.ecma-international.org/ecma-262/7.0/#sec-bitwise-not-operator): `~` (`Symbol.unaryOperator('~')`)
 
 - [Prefix/Postfix Unary Increment](https://www.ecma-international.org/ecma-262/7.0/#sec-prefix-increment-operator): `++` (`Symbol.unaryOperator('++')`)
 - [Prefix/Postfix Unary Decrement](https://www.ecma-international.org/ecma-262/7.0/#sec-prefix-decrement-operator): `--` (`Symbol.unaryOperator('--')`)
 - [Unary -](https://www.ecma-international.org/ecma-262/7.0/#sec-unary-minus-operator): `-` (`Symbol.unaryOperator('-')`)
 - [Unary +](https://www.ecma-international.org/ecma-262/7.0/#sec-unary-plus-operator): `+` (`Symbol.unaryOperator('+')`) (coerces return value `ToNumber`)
 - [Unary Logical NOT](https://www.ecma-international.org/ecma-262/7.0/#sec-logical-not-operator): `!` (`Symbol.unaryOperator('!')`) (coerces return value `ToBoolean`)
 - [Unary typeof](https://www.ecma-international.org/ecma-262/7.0/#sec-typeof-operator): `typeof` (`Symbol.unaryOperator('typeof')`) (coerces return value `ToString`)
 
 - [Relational >](https://www.ecma-international.org/ecma-262/7.0/#sec-relational-operators-runtime-semantics-evaluation): `>` (`Symbol.operator('>')`) (coerces return value `ToBoolean`)
 - [Relational <](https://www.ecma-international.org/ecma-262/7.0/#sec-relational-operators-runtime-semantics-evaluation): `<` (`Symbol.operator('<')`) (coerces return value `ToBoolean`)
 - [Relational >=](https://www.ecma-international.org/ecma-262/7.0/#sec-relational-operators-runtime-semantics-evaluation): `>=` (`Symbol.operator('>=')`) (coerces return value `ToBoolean`)
 - [Relational <=](https://www.ecma-international.org/ecma-262/7.0/#sec-relational-operators-runtime-semantics-evaluation): `<=` (`Symbol.operator('<=')`) (coerces return value `ToBoolean`)
 - [Loose equality](https://www.ecma-international.org/ecma-262/7.0/#sec-equality-operators-runtime-semantics-evaluation): `==` (`Symbol.operator('==')`) (coerces return value `ToBoolean`)

### Exceptions

The following operators are not supported:

 - Loose inequality Comparison: `!=` (`Symbol.operator('!=')`) (coerces return value `ToBoolean`)
   This is supported through negating the value of the _loose equality comparison_ (`==`) for consistency. 
 
 - Assignment operator: `=`. This is not supported in any way and behaviour will remain the same as older versions of the spec.
 
 - Strict equality/inequality comparison: `===`, `!==`. This is not supported in any way, and behaviour will remain the same as older versions of the spec.
   
 - Any compound assignment operator, e.g. `+=`, `-=`. These are supported through their non-compound equivalents, for example `a += b` will desugar to `a = a + b` which will in turn run the steps for the `+` operator. 
 
 - The `delete` operator. This is already dealt with via Proxies and `defineProperty`
 
 - The `void` operator. This discards the value and so is pointless to implement.
 
 - Logical `&&` or `||` operators. These are not supported in any way and behaviour will remain the same as older versions of the spec.

## Examples

### Example 1

```js
class Thinger() {
  constructor(...things) {
    this.things = things
  }
  
  static [Symbol.operator('+')](leftHandOperand, rightHandOperand) {
    return new Thinger(...[...leftHandOperand.things, ...rightHandOperand.things])
  }

}

const thingerA = Thinger('a')
const thingerB = Thinger('b')
const thingerAB = thingerA + thingerB
console.assert(thingerAB instanceof Thinger, 'the returned value from the `+` operator is a new Thinger instance')
console.assert(thingerAB.things[0] === 'a' && thingerAB.things[1] === 'b', 'the returned instance is created from the logic of the static method against both operands')

console.assert((thingerA + 1) === 1)
```


## Future: Extending of operators

Should one want to add an operator to the language, it could become as simple as implementing a new symbol a prototype; for example the [bind operator `::`](https://github.com/tc39/proposal-bind-operator) could simply be polyfilled with the following:

```js
Function[Symbol.operator('::')] = function (lho, rho) {
  return rho.bind(lho)
}
```

### How could this even work?

When parsing a piece of javascript, when the tokenizer reached an unrecognised token between one or two operands (with, perhaps a whitelist of certain allowed operator characters, for example any Unicode character betwixt U+2000–U+218F), it could search in the operator registry (perhaps at runtime? during evaluation?) for the operator, and if not found could raise a SyntaxError.