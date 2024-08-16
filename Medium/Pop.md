# 문제 설명

> 이 챌린지에는 TypeScript 4.0 사용이 권장됩니다.

배열 `T`를 사용해 마지막 요소를 제외한 배열을 반환하는 제네릭 `Pop<T>`를 구현합니다.

예시

```ts
type arr1 = ["a", "b", "c", "d"];
type arr2 = [3, 2, 1];

type re1 = Pop<arr1>; // expected to be ['a', 'b', 'c']
type re2 = Pop<arr2>; // expected to be [3, 2]
```

# 풀이

앞선 문제였던 Last 문제와 아주 유사한 문제였습니다. 타입을 추론하는 infer를 사용하여 해결합니다.

# solution

```ts
type Pop<T extends any[]> = T extends [...infer Rest, infer Last]
	? [...Rest]
	: [];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Pop<[3, 2, 1]>, [3, 2]>>,
	Expect<Equal<Pop<["a", "b", "c", "d"]>, ["a", "b", "c"]>>,
	Expect<Equal<Pop<[]>, []>>
];
```
