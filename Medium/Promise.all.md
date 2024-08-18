# 문제 설명

PromiseAll 함수를 작성하세요.
이 함수는 PromiseLike 객체들의 배열을 받아들이며, 반환되는 값은 T 타입의 결과 배열을 포함하는 Promise<T>여야 합니다.

```ts
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise<string>((resolve, reject) => {
	setTimeout(resolve, 100, "foo");
});

// 결과 타입은 `Promise<[number, 42, string]>`이어야 합니다.
const p = PromiseAll([promise1, promise2, promise3] as const);
```

# 풀이

```ts
declare function PromiseAll<T extends any[]>(
	values: [...T]
): Promise<{ [K in keyof T]: Awaited<T[K]> }>;
```

# solution

```ts
declare function PromiseAll<T extends any[]>(
	values: [...T]
): Promise<{ [K in keyof T]: Awaited<T[K]> }>;

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

const promiseAllTest1 = PromiseAll([1, 2, 3] as const);
const promiseAllTest2 = PromiseAll([1, 2, Promise.resolve(3)] as const);
const promiseAllTest3 = PromiseAll([1, 2, Promise.resolve(3)]);
const promiseAllTest4 = PromiseAll<Array<number | Promise<number>>>([1, 2, 3]);

type cases = [
	Expect<Equal<typeof promiseAllTest1, Promise<[1, 2, 3]>>>,
	Expect<Equal<typeof promiseAllTest2, Promise<[1, 2, number]>>>,
	Expect<Equal<typeof promiseAllTest3, Promise<[number, number, number]>>>,
	Expect<Equal<typeof promiseAllTest4, Promise<number[]>>>
];
```
