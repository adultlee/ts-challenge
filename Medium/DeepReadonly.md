# 문제

객체의 프로퍼티와 모든 하위 객체를 재귀적으로 읽기 전용으로 설정하는 제네릭 `DeepReadonly<T>`를 구현하세요.

이 챌린지에서는 타입 파라미터 `T`를 객체 타입으로 제한하고 있습니다. 객체뿐만 아니라 배열, 함수, 클래스 등 가능한 다양한 형태의 타입 파라미터를 사용하도록 도전해 보세요.

예시:

```ts
type X = {
	x: {
		a: 1;
		b: "hi";
	};
	y: "hey";
};

type Expected = {
	readonly x: {
		readonly a: 1;
		readonly b: "hi";
	};
	readonly y: "hey";
};

type Todo = DeepReadonly<X>; // should be same as `Expected`
```

# 풀이

```ts
type DeepReadonly<T> = {
	readonly [key in keyof T]: T[key] extends object
		? DeepReadonly<T[key]>
		: T[key];
};
```

```js
type Y1 = {
    readonly a: DeepReadonly<() => 22>;
    readonly b: string;
    readonly c: DeepReadonly<{
        d: boolean;
        e: {
            g: {
                h: {
                    i: true;
                    j: "string";
```

다음과 작성했지만 예외 케이스를 통과하지 못했습니다. 제귀의 형태가 다소 이상하게 진행됨을 확인할 수 있었습니다.
저는 그 이유가 정확히 object를 찾아낼 방법이 필요하다고 생각했습니다. (원시타입이 아닌지)

### keyof T[K] extends never

keyof는 객체 타입의 모든 키를 유니언 타입으로 반환합니다. 예를 들어, 객체 타입이 { a: string, b: number }라면, keyof { a: string, b: number }는 'a' | 'b'가 됩니다.

never는 TypeScript에서 어떤 값도 가질 수 없는 타입을 의미합니다. 예를 들어, 함수가 항상 예외를 던지거나 끝나지 않는 경우 그 반환 타입은 never가 됩니다. 또한, 빈 객체의 키 타입을 keyof로 추출하려고 하면 never가 됩니다

```ts
type DeepReadonly<T> = {
	readonly [K in keyof T]: keyof T[K] extends never ? T[K] : DeepReadonly<T[K]>;
};
```

```js
type Y1 = {
    readonly a: () => 22;
    readonly b: string;
    readonly c: DeepReadonly<{
        d: boolean;
        e: {
            g: {
                h: {
                    i: true;
                    j: "string";
```

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type DeepReadonly<T> = {
	readonly [key in keyof T]: keyof T[key] extends never
		? T[key]
		: DeepReadonly<T[key]>;
};

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<DeepReadonly<X1>, Expected1>>,
	Expect<Equal<DeepReadonly<X2>, Expected2>>
];

type X1 = {
	a: () => 22;
	b: string;
	c: {
		d: boolean;
		e: {
			g: {
				h: {
					i: true;
					j: "string";
				};
				k: "hello";
			};
			l: [
				"hi",
				{
					m: ["hey"];
				}
			];
		};
	};
};

type X2 = { a: string } | { b: number };

type Expected1 = {
	readonly a: () => 22;
	readonly b: string;
	readonly c: {
		readonly d: boolean;
		readonly e: {
			readonly g: {
				readonly h: {
					readonly i: true;
					readonly j: "string";
				};
				readonly k: "hello";
			};
			readonly l: readonly [
				"hi",
				{
					readonly m: readonly ["hey"];
				}
			];
		};
	};
};

type Expected2 = { readonly a: string } | { readonly b: number };
```
