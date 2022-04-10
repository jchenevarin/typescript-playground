# Typescript presentation


# prerequisites

```
npm install -g typescript
tsc --init
```

# DAY1

- noEmitOnError prevents tsc from outputting js if errors were to be found
- noImplicitAny prevents the dev to access a any property if it wasn't explicitly declared as any. Doing so tells typescript to simply ignore any access on the any typed object.
- When strictNullChecks is true, null and undefined have their own distinct types and you’ll get a type error if you try to use them where a concrete value is expected.
- maximum checking can be turned on with the "strict": true option in the tsconfig.json
- you can start building new types using 'type alias' based on the existing types and perform type narrowing using 'typeOf' operator.You can cast types with the 'as' keyword or the <type> bracket expression. When required we might need to go the  hard way with 'const a = (expr as any) as T;'
- literal types are usually associated to const declarations. TS will infer these values are subtypes of string or number for instance. using let declaration operator does not lead to literal typing because the variable ca be re-assigned;
- use the 'as const' expression to explicitly use literal typing like 'const req = { url: "https://example.com", method: "GET" } as const;' TS will use literal typing instead of generic typing.
- Almost all features of an interface are available in type, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable

# DAY2

## Type narrowing
- typeof is a good way to narrow type of a param. Here are the available type:
"string"
"number"
"bigint"
"boolean"
"symbol"
"undefined"
"object"
"function"

- loose equality operator == null or ==undefined checks for both null & undefined each ! in regular JS
- typeof is the operator to check for type
- you can also use the in operator like if ("prop" in object), TS will now
- instanceof is also used to type narrow a type
- the properway TS way of type narrowing is probably to declare a user-defined type guard

```ts
function isHuman(guy: Human | Robot): guy is Robot {
    return (guy as Robot).processor !== undefined;
}
```

- sometimes we can use the non-null assertion operator ! to tell TS I know this props exists
- discriminated union is like both type share a same literal typed prop. Narrowing on that prop could elp TS guess what's the underlying type.
- the never type and exhaustivness checking, seriously.

```ts
type Circle = { kind: 'circle', radius: number };
type Square = { kind: 'square', sideLength: number };
type Shape = Circle | Square;

function getArea(shape: Shape) {
    switch (shape.kind) {
        case "circle":
        return Math.PI * shape.radius ** 2;
        case "square":
        return shape.sideLength ** 2;
        default:
        const _exhaustiveCheck: never = shape;
        return _exhaustiveCheck;
    }
}

function fail(msg: string): never {
  throw new Error(msg);
}
```

## TS for functions

- you can define type alias for functions:
```ts
type GreeterFunction = (s: string) => void;
```

- for construct signatures add the `new` keyword
```ts
interface CallOrConstruct {
  new (s: string): Date;
  (n?: number): number;
}
```

- for generic functions
```ts
function first<T>(list: T[]): T | undefined {
  return list[0];
}
const a = first(['a','b','c']);
const b = first([1,2,3]);
```

- optional function parameter can be specified with a ?, this is equivalent of `type | undefined`

- extra parameters of functions are ignored in both JS and TS.

# DAY 3

- function overloads ! Below 2 functions overloads followed by the implementation. This is quite confusing, use with caution. Always prefer parameters with union types instead of overloads when possible

```ts
function sayHello(firstName: string): string;
function sayHello(firstName: string, lastName: string): string;
function sayHello(firstName: string, lastName?: string, chill?: boolean): string {
  const greetings = chill ? 'Hi': 'Hello';
  return `${greetings} ${firstName} ${lastName}`
}

```

- The special type object refers to any value that isn’t a primitive (string, number, bigint, boolean, symbol, null, or undefined). This is different from the empty object type { }, and also different from the global type Object. It’s very likely you will never use Object.

object is not Object. Always use object!

Note that in JavaScript, function values are objects: They have properties, have Object.prototype in their prototype chain, are instanceof Object, you can call Object.keys on them, and so on. For this reason, function types are considered to be objects in TypeScript.

- there's a type Function which has bind, call, apply properties. it has a void return type and is better being avoided, prefer `() => void` signature

```ts
function doSomething(f: Function) {
  return f(1, 2, 3);
}
```

- using the rest operator cause TS to infer a `any[]` type

- typing destructuration looks like this:

```ts
function sum({ a, b, c }: { a: number; b: number; c: number }) {
  console.log(a + b + c);
}
```

- `this` has a special meaning in function signature. In a method or function definition, an initial parameter named this has special meaning in TypeScript. These parameters are erased during compilation:


# DAY 4

https://www.typescriptlang.org/docs/handbook/2/objects.html



## Type manipulation 

- `typeof parameter` returns the type of any value

- `ReturnType<function_type>` returns the type of the function return

- `AwaitedType<Promise<boolean>>` returns boolean type

- indexed types is used to get the type of a embeded property

```ts
type age = "age";
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"];
// or
type Age = Person[age];
```

# DAY 5

## Type utility

- Partial<Type> constructs a type with all properties optional 
- Required<Type> constructs a type with all properties required
- NonNullable<Type> constructs a type retaining only non-nullabe properties
- Readonly<Type> constructs a type with all properties readonly.

```ts
function freeze<Type>(obj: Type): Readonly<Type>;
```

- Record<Keys, Type> not sure about the usage
- Pick<Type, Keys> constructs a subtype
- Omit<Type, Keys> constructs a subtype

- Exclude<Type, ExcludedUnion>
- Extract<Type, ExcludedUnion>

- Parameters<Type>
- ReturnType<Type>

```ts
declare function f1(arg: { a: number; b: string }): string;
type T1 = Parameters(typeof f1); 

// returns
type T1 = [arg: {
    a: number;
    b: string;
}] 

type T2 = Parameters(typeof f1); 
// returns
type T2 = string

```

# Resources
https://www.typescriptlang.org/docs/handbook/intro.html
https://github.com/dzharii/awesome-typescript
https://github.com/typescript-cheatsheets/react

on testing:
https://medium.com/welldone-software/an-overview-of-javascript-testing-7ce7298b9870


