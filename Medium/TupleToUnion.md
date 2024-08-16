# 문제설명

튜플 값으로 유니온 타입을 생성하는 제네릭 `TupleToUnion<T>`를 구현하세요.

예시:

```ts
type Arr = ["1", "2", "3"];

type Test = TupleToUnion<Arr>; // expected to be '1' | '2' | '3'
```

# 풀이

마찬가지로 크게 어려운 문제는 아니었습니다. Tuple to object 문제와 유사하게 문제를 풀이하면 됩니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type TupleToUnion<T extends any[]> = T[number];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<TupleToUnion<[123, "456", true]>, 123 | "456" | true>>,
	Expect<Equal<TupleToUnion<[123]>, 123>>
];
```
