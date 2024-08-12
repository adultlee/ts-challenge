# 문제 설명

`T`에서 `U`에 할당할 수 있는 타입을 제외하는 내장 제네릭 `Exclude<T, U>`를 이를 사용하지 않고 구현하세요.

예시:

```ts
type Result = MyExclude<"a" | "b" | "c", "a">; // 'b' | 'c'
```

# 풀이

해당 문제는 조건문 타입 내에서의 extends에 대해서 이해하면 쉽게 풀이할 수 있습니다.

조건부 타입은 T extends U ? X : Y의 형태를 가지며, 이는 T가 U에 할당 가능하면 X 타입을 반환하고, 그렇지 않으면 Y 타입을 반환하는 타입입니다. (여기서 T 혹은 U를 사용하게 된다면, 해당 확인 단계에 포함한 인자를 넣게 됩니다. for in 문과 유사합니다.)

```ts
type MyExclude<T, U> = T extends U ? never : T;
```

# solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyExclude<T, U> = T extends U ? never : T;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<MyExclude<"a" | "b" | "c", "a">, "b" | "c">>,
	Expect<Equal<MyExclude<"a" | "b" | "c", "a" | "b">, "c">>,
	Expect<
		Equal<MyExclude<string | number | (() => void), Function>, string | number>
	>
];
```
