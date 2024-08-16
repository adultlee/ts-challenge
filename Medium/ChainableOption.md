# 문제 설명

체인 가능 옵션은 일반적으로 Javascript에서 사용됩니다. 하지만 TypeScript로 전환하면 제대로 구현할 수 있나요?

이 챌린지에서는 `option(key, value)`과 `get()` 두가지 함수를 제공하는 객체(또는 클래스) 타입을 구현해야 합니다. 현재 타입을 `option`으로 지정된 키와 값으로 확장할 수 있고 `get`으로 최종 결과를 가져올 수 있어야 합니다.

예시

```ts
declare const config: Chainable;

const result = config
	.option("foo", 123)
	.option("name", "type-challenges")
	.option("bar", { value: "Hello World" })
	.get();

// 결과는 다음과 같습니다:
interface Result {
	foo: number;
	name: string;
	bar: {
		value: string;
	};
}
```

문제를 해결하기 위해 js/ts 로직을 작성할 필요는 없습니다. 단지 타입 수준입니다.

`key`는 `string`만 허용하고 `value`는 무엇이든 될 수 있다고 가정합니다. 같은 `key`는 두 번 전달되지 않습니다.

# 풀이

### options 메서드를 추가하여야

```ts
type Chainable<T = {}> = {
	option: <K extends string, V>(
		key: K extends keyof T ? (V extends T[K] ? never : K) : K,
		value: V
	) => Chainable<Omit<T, K> & Record<K, V>>;
	get: () => T;
};
```

`<K extends string, V>` 와 같은 부분은 ts에서 제네릭을 선언하는 부분입니다. 이 구문은 함수의 제네릭 타입 매개변수를 추가하여, 해당 함수가 다양한 타입에 대해 재사용 가능하도록 합니다.
V는 어떤 타입이든 될 수 있습니다. 제약이 없기 때문에, 이 타입 매개변수는 함수가 호출될 때 어떤 타입으로도 설정될 수 있습니다.
제네릭 타입을 사용하는 이유는 제네릭을 사용하면 함수나 클래스가 다양한 타입에 대해 동작할 수 있기 때문입니다.

# solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Chainable<T = object> = {
	option: <K extends string, V>(
		key: K extends keyof T ? never : K,
		value: V
	) => Chainable<Omit<T, K> & Record<K, V>>;
	get: () => T;
};

/* _____________ 테스트 케이스 _____________ */
import type { Alike, Expect } from "@type-challenges/utils";

declare const a: Chainable;

const result1 = a
	.option("foo", 123)
	.option("bar", { value: "Hello World" })
	.option("name", "type-challenges")
	.get();

const result2 = a
	.option("name", "another name")
	// @ts-expect-error
	.option("name", "last name")
	.get();

const result3 = a
	.option("name", "another name")
	// @ts-expect-error
	.option("name", 123)
	.get();

type cases = [
	Expect<Alike<typeof result1, Expected1>>,
	Expect<Alike<typeof result2, Expected2>>,
	Expect<Alike<typeof result3, Expected3>>
];

type Expected1 = {
	foo: number;
	bar: {
		value: string;
	};
	name: string;
};

type Expected2 = {
	name: string;
};

type Expected3 = {
	name: number;
};
```
