# 문제 설명

내장 제네릭 `ReturnType<T>`을 이를 사용하지 않고 구현하세요.

예시:

```ts
const fn = (v: boolean) => {
	if (v) return 1;
	else return 2;
};

type a = MyReturnType<typeof fn>; // should be "1 | 2"
```

# 풀이

```ts
type MyReturnType<T> = T extends (...args: any) => infer R ? R : never;
```

크게 어렵지 않았습니다. 다만 extends 뒤의 (...args)를 작성했어야 했는데 단일 parameter를 작성(args)하여 조금 문제가 발생했었습니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyReturnType<T> = T extends (...args: any) => infer R ? R : never;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<string, MyReturnType<() => string>>>,
	Expect<Equal<123, MyReturnType<() => 123>>>,
	Expect<Equal<ComplexObject, MyReturnType<() => ComplexObject>>>,
	Expect<Equal<Promise<boolean>, MyReturnType<() => Promise<boolean>>>>,
	Expect<Equal<() => "foo", MyReturnType<() => () => "foo">>>,
	Expect<Equal<1 | 2, MyReturnType<typeof fn>>>,
	Expect<Equal<1 | 2, MyReturnType<typeof fn1>>>
];

type ComplexObject = {
	a: [12, "foo"];
	bar: "hello";
	prev(): number;
};
```
