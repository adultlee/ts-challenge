# 문제설명

`T`에서 `K` 프로퍼티만 읽기 전용으로 설정해 새로운 오브젝트 타입을 만드는 제네릭 `MyReadonly2<T, K>`를 구현하세요. `K`가 주어지지 않으면 단순히 `Readonly<T>`처럼 모든 프로퍼티를 읽기 전용으로 설정해야 합니다.

예시:

```ts
interface Todo {
	title: string;
	description: string;
	completed: boolean;
}

const todo: MyReadonly2<Todo, "title" | "description"> = {
	title: "Hey",
	description: "foobar",
	completed: false,
};

todo.title = "Hello"; // Error: cannot reassign a readonly property
todo.description = "barFoo"; // Error: cannot reassign a readonly property
todo.completed = true; // OK
```

# 풀이

```ts
type MyReadonly2<T, K> = {
	[key in keyof T as key extends K ? readonly key : key]: T[key];
};
```

마치 이런 형태로 사용하고 싶은데, 잘 변경해보면

```ts
type MyReadonly2<T, K extends keyof T = keyof T> = {
	// 기본타입 설정 아주 중요
	readonly [P in K]: T[P];
} & {
	[P in keyof T as P extends K ? never : P]: T[P];
};
```

K는 T의 key값을 받는 유니온 타입으로 변경하고, 기본값을 설정합니다.
그 이후 K에 포함된 P라는 key에 대해서 readonly를 설정하고 나머지 값들과 결합합니다.

그후 나머지 값들에 대해서 연산합니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyReadonly2<T, K extends keyof T = keyof T> = {
	readonly [P in K]: T[P];
} & {
	[P in keyof T as P extends K ? never : P]: T[P];
};
/* _____________ 테스트 케이스 _____________ */
import type { Alike, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Alike<MyReadonly2<Todo1>, Readonly<Todo1>>>,
	Expect<Alike<MyReadonly2<Todo1, "title" | "description">, Expected>>,
	Expect<Alike<MyReadonly2<Todo2, "title" | "description">, Expected>>,
	Expect<Alike<MyReadonly2<Todo2, "description">, Expected>>
];

// @ts-expect-error
type error = MyReadonly2<Todo1, "title" | "invalid">;

interface Todo1 {
	title: string;
	description?: string;
	completed: boolean;
}

interface Todo2 {
	readonly title: string;
	description?: string;
	completed: boolean;
}

interface Expected {
	readonly title: string;
	readonly description?: string;
	completed: boolean;
}
```
