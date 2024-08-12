# 문제 설명

# 풀이

```ts
type MyOmit<T, K> = T extends K ? never : T;
```

다음과 같이 작성하였으나 이건 Excludes 처럼의 동작과 같아서 배제합니다. 오히려 Pick과 비슷하게 구현해야했습니다.

```ts
type MyOmit<T, K> = { [key in keyof T as key extends K ? never : key]: T[key] };
```

다소 복잡하지만 하나씩 하나씩 풀어서 해결해봅시다.

1. keyof T는 객체 타입 T의 모든 키를 가져오는 TypeScript의 내장 기능입니다.
2. [key in keyof T]는 T의 모든 키를 순회하면서, 새로운 객체 타입을 만드는 구문입니다.
3. key extends K ? never : key 는 key가 K에 포함되는지 (extends K)를 검사하고, 만약 포함된다면 never를 반환합니다. 그렇지 않다면 key를 그대로 사용합니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyOmit<T, K> = { [key in keyof T as key extends K ? never : key]: T[key] };

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Expected1, MyOmit<Todo, "description">>>,
	Expect<Equal<Expected2, MyOmit<Todo, "description" | "completed">>>,
	Expect<Equal<Expected3, MyOmit<Todo1, "description" | "completed">>>
];

// @ts-expect-error
type error = MyOmit<Todo, "description" | "invalid">;

interface Todo {
	title: string;
	description: string;
	completed: boolean;
}

interface Todo1 {
	readonly title: string;
	description: string;
	completed: boolean;
}

interface Expected1 {
	title: string;
	completed: boolean;
}

interface Expected2 {
	title: string;
}

interface Expected3 {
	readonly title: string;
}
```
