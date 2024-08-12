# 문제설명

JavaScript의 `Array.concat` 함수를 타입 시스템에서 구현하세요. 타입은 두 인수를 받고, 인수를 왼쪽부터 concat한 새로운 배열을 반환해야 합니다.

예시:

```ts
type Result = Concat<[1], [2]>; // expected to be [1, 2]
```

# 풀이

```ts
type Concat<T extends readonly any[], U extends readonly any[]> = [...T, ...U];
```

array의 spread method를 사용하여 어렵지 않게 해결할 수 있었습니다!

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Concat<T extends readonly any[], U extends readonly any[]> = [...T, ...U];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

const tuple = [1] as const;

type cases = [
	Expect<Equal<Concat<[], []>, []>>,
	Expect<Equal<Concat<[], [1]>, [1]>>,
	Expect<Equal<Concat<typeof tuple, typeof tuple>, [1, 1]>>,
	Expect<Equal<Concat<[1, 2], [3, 4]>, [1, 2, 3, 4]>>,
	Expect<
		Equal<
			Concat<["1", 2, "3"], [false, boolean, "4"]>,
			["1", 2, "3", false, boolean, "4"]
		>
	>
];
```
