# 문제설명

Promise와 같은 타입에 감싸인 타입이 있을 때, 안에 감싸인 타입이 무엇인지 어떻게 알 수 있을까요?

예시: 들어 `Promise<ExampleType>`이 있을 때, `ExampleType`을 어떻게 얻을 수 있을까요?

```ts
type ExampleType = Promise<string>;

type Result = MyAwaited<ExampleType>; // string
```

# 풀이

```ts
type MyAwaited<T> = T extends Promise<infer U> ? U : never;
```

처음에는 다음과 같이 작성하여 문제를 풀었습니다. infer를 사용하여 조건절에서의 타입을 예측해 추출합니다.
하지만 제귀적으로 Promise내부에 Promise가 선언되어 있는 test가 있어 수정했습니다.

```ts
type MyAwaited<T> = T extends Promise<infer U>
	? U extends Promise<any>
		? MyAwaited<U>
		: U
	: T;
```

하지만 마지막 test에서 문제가 발생했습니다.

```ts
type T = { then: (onfulfilled: (arg: number) => any) => any }; // 문제 발생
```

다음과 같은 예시에서는 Promise로는 해결되지 않습니다. 따라서 then을 포함하는 메서드인 `PromiseLike`를 사용합니다

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyAwaited<T extends PromiseLike<any>> = T extends PromiseLike<infer U>
	? U extends PromiseLike<any>
		? MyAwaited<U>
		: U
	: never;
/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type X = Promise<string>;
type Y = Promise<{ field: number }>;
type Z = Promise<Promise<string | number>>;
type Z1 = Promise<Promise<Promise<string | boolean>>>;
type T = { then: (onfulfilled: (arg: number) => any) => any };

type cases = [
	Expect<Equal<MyAwaited<X>, string>>,
	Expect<Equal<MyAwaited<Y>, { field: number }>>,
	Expect<Equal<MyAwaited<Z>, string | number>>,
	Expect<Equal<MyAwaited<Z1>, string | boolean>>,
	Expect<Equal<MyAwaited<T>, number>>
];
```
