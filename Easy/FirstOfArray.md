# 문제 설명

배열(튜플) T를 받아 첫 원소의 타입을 반환하는 제네릭 First<T>를 구현하세요.

```ts
type arr1 = ["a", "b", "c"];
type arr2 = [3, 2, 1];

type head1 = First<arr1>; // expected to be 'a'
type head2 = First<arr2>; // expected to be 3
```

# 풀이

```ts
type First<T extends any[]> = T[0];
```

보자마자 시도한 방법입니다만

```ts
  Expect<Equal<First<[]>, never>>,
```

해당 구문에서 에러가 발생했습니다.

[] 빈배열일때의 예외처리가 되지 않아서 였으므로, 해당 구문에 대한 예외처리를 진행합니다.

```ts
type First<T extends any[]> = T extends [] ? never : T[0];
```

`T extends [] ? never : T[0]` T가 빈 배열인지 확인하고, 빈 배열인 경우 never를 반환합니다. 그렇지 않으면 T[0] 즉, 첫 번째 요소의 타입을 반환합니다.

# Solution

```ts
type First<T extends any[]> = T extends [] ? never : T[0];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<First<[3, 2, 1]>, 3>>,
	Expect<Equal<First<[() => 123, { a: string }]>, () => 123>>,
	Expect<Equal<First<[]>, never>>,
	Expect<Equal<First<[undefined]>, undefined>>
];

type errors = [
	// @ts-expect-error
	First<"notArray">,
	// @ts-expect-error
	First<{ 0: "arrayLike" }>
];
```
