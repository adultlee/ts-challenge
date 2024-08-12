# 문제 설명

JavaScript의 `Array.includes` 함수를 타입 시스템에서 구현하세요. 타입은 두 인수를 받고, `true` 또는 `false`를 반환해야 합니다.

예시:

```ts
type isPillarMen = Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Dio">; // expected to be `false`
```

# 풀이

보자마자 시도한 방법은 다음과 같습니다.

```ts
type Includes<T extends readonly any[], U> = U extends T[number] ? true : false;
```

하지만 다음과 같은 에러가 발생했습니다.

```ts
Includes<[{}], { a: "A" }>; // true...?
```

해당 타입이 true 가 나오는것이 이상했습니다. 그 이유는 {} 타입은 "빈 객체 타입"으로, 모든 객체 타입의 상위 타입(supertype)으로 간주되기 때문입니다.

### {} 타입의 의미

TypeScript에서 {} 타입은 **"빈 객체 타입"**으로 알려져 있습니다. 이는 객체를 의미하지만, 그 객체에 어떤 특정한 속성이 있음을 명시하지 않은 타입입니다.

{} 타입은 사실상 모든 JavaScript 값과 호환됩니다. 이는 숫자, 문자열, 배열, 함수, 객체, null, undefined를 포함한 모든 값이 {} 타입에 할당될 수 있음을 의미합니다.

```ts
let emptyObject: {} = {}; // 빈 객체 할당 가능
let numberValue: {} = 123; // 숫자 할당 가능
let stringValue: {} = "Hello"; // 문자열 할당 가능
let arrayValue: {} = [1, 2, 3]; // 배열 할당 가능
let functionValue: {} = () => {}; // 함수 할당 가능

// 더 구체적인 객체 타입에도 할당 가능
let specificObject: {} = { a: "hello", b: 42 };
```

> object 타입은 원시 값(primitive types)을 허용하지 않지만, {} 타입은 원시 값도 허용합니다. 예를 들어, number나 string도 {} 타입에 할당될 수 있지만, object 타입에는 할당될 수 없습니다.

따라서 {} 내부에서도 확인하여 완벽히 호환하는지 확인할 필요가 있었습니다.

### 제귀적 풀이

그렇다면 제귀적인 방식으로 이런건 어떨까요? 저희가 DeepReadonly 를 만들어서 제귀적으로 문제를 해결했던것처럼 (저의 readonly에 풀이가 적혀 있습니다.) 이번에도 제귀적으로 문제를 풀어보겠습니다.

```ts
type Includes<Value extends any[], Item> = Value extends [
	infer First,
	...infer Rest
]
	? Equal<First, Item> extends true // Equal을 사용해보았습니다! 당연히 사용하면 안되겠지만 말이죠.
		? true
		: Includes<Rest, Item>
	: false;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<
		Equal<Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Kars">, true>
	>,
	Expect<
		Equal<Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Dio">, false>
	>,
	Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 7>, true>>,
	Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 4>, false>>,
	Expect<Equal<Includes<[1, 2, 3], 2>, true>>,
	Expect<Equal<Includes<[1, 2, 3], 1>, true>>,
	Expect<Equal<Includes<[{}], { a: "A" }>, false>>,
	Expect<Equal<Includes<[boolean, 2, 3, 5, 6, 7], false>, false>>,
	Expect<Equal<Includes<[true, 2, 3, 5, 6, 7], boolean>, false>>,
	Expect<Equal<Includes<[false, 2, 3, 5, 6, 7], false>, true>>,
	Expect<Equal<Includes<[{ a: "A" }], { readonly a: "A" }>, false>>,
	Expect<Equal<Includes<[{ readonly a: "A" }], { a: "A" }>, false>>,
	Expect<Equal<Includes<[1], 1 | 2>, false>>,
	Expect<Equal<Includes<[1 | 2], 1>, false>>,
	Expect<Equal<Includes<[null], undefined>, false>>,
	Expect<Equal<Includes<[undefined], null>, false>>
];
```

문제없이 모두 잘 반환하는것을 확인할 수 있었습니다.
그렇다면 이제 Equal을 대신할 수 있는 함수를 만들면 되겠군요. 안타깝게도 Equal을 대신하는 제네릭은 없으니 직접 구현을 통해 만들어 주어야 합니다.

```ts
/**
Returns a boolean for whether given two types are equal.
@link https://github.com/microsoft/TypeScript/issues/27024#issuecomment-421529650
*/
type IsEqual<T, U> = (<G>() => G extends T ? 1 : 2) extends <G>() => G extends U
	? 1
	: 2
	? true
	: false;
```

다음과 같이 ts issues에 포함되어 있는 IsEqual을 사용해보았습니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type IsEqual<T, U> = (<G>() => G extends T ? 1 : 2) extends <G>() => G extends U
	? 1
	: 2
	? true
	: false;

type Includes<Value extends any[], Item> = Value extends [
	infer First,
	...infer Rest
]
	? IsEqual<First, Item> extends true
		? true
		: Includes<Rest, Item>
	: false;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<
		Equal<Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Kars">, true>
	>,
	Expect<
		Equal<Includes<["Kars", "Esidisi", "Wamuu", "Santana"], "Dio">, false>
	>,
	Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 7>, true>>,
	Expect<Equal<Includes<[1, 2, 3, 5, 6, 7], 4>, false>>,
	Expect<Equal<Includes<[1, 2, 3], 2>, true>>,
	Expect<Equal<Includes<[1, 2, 3], 1>, true>>,
	Expect<Equal<Includes<[{}], { a: "A" }>, false>>,
	Expect<Equal<Includes<[boolean, 2, 3, 5, 6, 7], false>, false>>,
	Expect<Equal<Includes<[true, 2, 3, 5, 6, 7], boolean>, false>>,
	Expect<Equal<Includes<[false, 2, 3, 5, 6, 7], false>, true>>,
	Expect<Equal<Includes<[{ a: "A" }], { readonly a: "A" }>, false>>,
	Expect<Equal<Includes<[{ readonly a: "A" }], { a: "A" }>, false>>,
	Expect<Equal<Includes<[1], 1 | 2>, false>>,
	Expect<Equal<Includes<[1 | 2], 1>, false>>,
	Expect<Equal<Includes<[null], undefined>, false>>,
	Expect<Equal<Includes<[undefined], null>, false>>
];
```
