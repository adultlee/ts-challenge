# 문제 설명

`Array.push`의 제네릭 버전을 구현하세요.

예시:

```typescript
type Result = Push<[1, 2], "3">; // [1, 2, '3']
```

# 풀이

이 또한 어려운 문제는 아니었습니다. 문제중 하나인, unshift 문제를 해결했다면, 동시에 해결할 수 있는 문제였습니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Push<T extends any[], U> = [...T, U];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Push<[], 1>, [1]>>,
	Expect<Equal<Push<[1, 2], "3">, [1, 2, "3"]>>,
	Expect<Equal<Push<["1", 2, "3"], boolean>, ["1", 2, "3", boolean]>>
];
```
