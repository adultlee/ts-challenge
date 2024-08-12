# 문제 설명

내장 제네릭 `Parameters<T>`를 이를 사용하지 않고 구현하세요.

예시:

```ts
const foo = (arg1: string, arg2: number): void => {};

type FunctionParamsType = MyParameters<typeof foo>; // [arg1: string, arg2: number]
```

# 풀이

내장 제네릭 Parameters<T>는 함수 타입 T의 매개변수(parameter) 타입들을 튜플(tuple) 형태로 추출하는 데 사용됩니다.

```ts
type MyParameters<T extends (...args: any[]) => any> = T extends (
	...args: infer P
) => any
	? P
	: never;
```

해당 구문에서 infer 키워드를 사용할때 infer로 추론된 타입이 배열 형태로 나오는 이유는, 함수의 매개변수들이 튜플(배열) 타입으로 표현되기 때문입니다.  
 TypeScript에서 함수의 매개변수 목록은 내부적으로 튜플 타입으로 표현되며, 이 때문에 infer를 통해 추론된 매개변수 타입이 배열(튜플) 형태가 되는 것입니다.

#### 튜플과 어레이

```ts
// 튜플은 정해진 수의 요소를 가지며, 각 요소의 타입이 고정되어 있습니다. 즉, 각 위치에 어떤 타입의 값이 들어가는지 미리 정해져 있습니다.
// 튜플은 정의된 길이만큼의 요소를 가집니다. 길이가 고정되어 있어, 추가하거나 요소를 생략할 수 없습니다.
let tuple: [string, number];
tuple = ["hello", 42]; // 올바른 사용
tuple = [42, "hello"]; // 오류: [string, number] 타입이 아님
tuple = ["hello"]; // 오류: 두 번째 요소가 없어서 오류 발생

// 배열의 길이는 고정되어 있지 않으며, 요소를 추가하거나 제거할 수 있습니다.
// 배열은 동일한 타입의 요소를 가변적으로 가질 수 있습니다. 배열의 길이는 고정되어 있지 않으며, 요소의 타입은 보통 하나의 타입(또는 유니언 타입)으로 정의됩니다.
let array: number[];
array = [1, 2, 3]; // 올바른 사용
array.push(4); // 요소 추가 가능
array.pop(); // 요소 제거 가능
```

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type MyParameters<T extends (...args: any[]) => any> = T extends (
	...args: infer P
) => any
	? P
	: never;

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

function foo(arg1: string, arg2: number): void {}
function bar(arg1: boolean, arg2: { a: "A" }): void {}
function baz(): void {}

type cases = [
	Expect<Equal<MyParameters<typeof foo>, [string, number]>>,
	Expect<Equal<MyParameters<typeof bar>, [boolean, { a: "A" }]>>,
	Expect<Equal<MyParameters<typeof baz>, []>>
];
```
