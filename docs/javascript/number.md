## Number
Whenever you are handling numbers in any programming language you need to be aware of the idiosyncrasies of how the language handles numbers. Here are few critical pieces of information about numbers in JavaScript that you should be aware of.

### Core Type
JavaScript has only one number type. It is a double-precision 64-bit `Number`. Below we discuss its limitations along with a recommended solution.

### Decimal
For those familiar with doubles / float in other languages, you would know that binary floating point numbers *do not* map correctly to Decimal numbers. A trivial (and famous) example with JavaScript's built in numbers is shown below:

```js
console.log(.1 + .2); // 0.30000000000000004
```

> For true decimal math use `big.js` mentioned below.

### Integer
The integer limits represented by the built in number type are `Number.MAX_SAFE_INTEGER` and `Number.MIN_SAFE_INTEGER`.

```js
console.log({max: Number.MAX_SAFE_INTEGER, min: Number.MIN_SAFE_INTEGER});
// {max: 9007199254740991, min: -9007199254740991}
```

**Safe** in this context refers to the ability to still carry out `1` more arithmetic calculation.

```js
console.log(Number.MAX_SAFE_INTEGER + 1 === Number.MAX_SAFE_INTEGER); // false
console.log(Number.MIN_SAFE_INTEGER - 1 === Number.MIN_SAFE_INTEGER); // false
```

Out of bound (`2` or more) integer arithmetic is cut off to these max values (`+1/-1`):

```js
console.log(Number.MAX_SAFE_INTEGER + 2 === Number.MAX_SAFE_INTEGER + 1); // true!
console.log(Number.MIN_SAFE_INTEGER - 2 === Number.MIN_SAFE_INTEGER - 1); // true!
```

> For arbitrary precision integer math use `big.js` mentioned below.

### big.js
Whenever you use math for financial calculations (e.g. GST calculation, money with cents addition etc) use a library like [big.js](https://github.com/MikeMcl/big.js/) which is designed for
* Perfect decimal math.
* Safe out of bound integer values

Installation is simple:
```bash
npm install big.js @types/big.js
```

Quick Usage example: 

```js
import { Big } from 'big.js';

export const foo = new Big('111.11111111111111111111');
export const bar = foo.plus(new Big('0.00000000000000000001'));

// To get a number: 
const x: number = Number(bar.toString()); // Looses the precision
```

> Do not use this library for math used for UI / performance intensive purposes e.g charts, canvas drawing etc.

### NaN
When some number calculation is not representable by a valid number, JavaScript returns a special `NaN` value. A  classic example is imaginary numbers:

```js
console.log(Math.sqrt(-1)); // NaN
```

Note: Equality checks **don't** work on `NaN` values. Instead use `Number.isNaN` instead:

```js
// Don't do this
console.log(NaN === NaN); // false!!

// Do this
console.log(Number.isNaN(NaN)); // true
```

### Infinity
The outer bounds of values representable in Number are available as static `Number.MAX_VALUE` and `Number.MIN_VALUE` values.

```js
console.log(Number.MAX_VALUE); // 1.7976931348623157e+308
console.log(Number.MIN_VALUE); // 5e-324
```

Values outside the range where precision isn't changed are clamped to these limits e.g.

```js
console.log(Number.MAX_VALUE + 1 == Number.MAX_VALUE); // true!
console.log(Number.MIN_VALUE - 1 == Number.MIN_VALUE); // true!
```

Values outside the range where precision is changed resolve to special values `Infinity`/`-Infinity` e.g.

```js
console.log(Number.MAX_VALUE + 10**1000); // Infinity
console.log(Number.MIN_VALUE - 10**1000); // -Infinity
```

Of-course, these special infinity values also show up with arithmetic that requires it e.g.

```js
console.log( 1 / 0); // Infinity
console.log(-1 / 0); // -Infinity
```

You can use these `Infinity` values manually or using static members of the `Number` class as shown below:

```js
console.log(Number.POSITIVE_INFINITY === Infinity); // true
console.log(Number.NEGATIVE_INFINITY === -Infinity); // true
```

Fortunately comparison operators (`<` / `>`) work reliably on infinity values:

```js
console.log( Infinity >  1); // true
console.log(-Infinity < -1); // true
```
