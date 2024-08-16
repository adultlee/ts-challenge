# 문제설명

> 이 챌린지에는 TypeScript 4.0 사용이 권장됩니다.

배열 `T`를 사용하고 마지막 요소를 반환하는 제네릭 `Last<T>`를 구현합니다.

예시

```ts
type arr1 = ["a", "b", "c"];
type arr2 = [3, 2, 1];

type tail1 = Last<arr1>; // expected to be 'c'
type tail2 = Last<arr2>; // expected to be 1
```

# 풀이

```ts
type Last<T extends any[]> = T[T["length"] - 1];
```

처음에는 다음과 같은 방식으로 접근했습니다. 하지만 타입스크립트에서는 타입시스템이 정수 "연산"을 지원하지 않기 떄문에, T["length"]-1을 접근이 안되는 문제가 있었습니다. 따라서 다른 방법이 필요했습니다.

```ts
type Last<T extends any[]> = T extends [...infer Rest, infer Last]
	? Last
	: never;
```

# solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Last<T extends any[]> = T extends [...infer Rest, infer Last]
	? Last
	: never;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Last<[2]>, 2>>,
	Expect<Equal<Last<[3, 2, 1]>, 1>>,
	Expect<Equal<Last<[() => 123, { a: string }]>, { a: string }>>
];
```
