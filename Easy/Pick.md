# 문제 설명

`T`에서 `K` 프로퍼티만 선택해 새로운 오브젝트 타입을 만드는 내장 제네릭 `Pick<T, K>`을 이를 사용하지 않고 구현하세요.

예시:

```ts
interface Todo {
	title: string;
	description: string;
	completed: boolean;
}

type TodoPreview = MyPick<Todo, "title" | "completed">;

const todo: TodoPreview = {
	title: "Clean room",
	completed: false,
};
```

# 풀이

`Pick<T, K>` 는 굉장히 강력한 제네릭 기능입니다. 해당 내장 제네릭 타입을 이용하여
기존 타입을 기반으로 일부 프로퍼티만 필요로 하는 경우에 새로운 타입을 정의할 때 사용됩니다.

이번 문제는 `Pick<T, K>`를 대신하는 기능을 직접 구현해볼 것입니다.
여기서 extends 키워드와, key-value 매핑을 통해서 구현을 해보겠습니다.

### extends

Typescript 에서 extends 키워드를 가장 쉽게 이해하는 방법은 "상속"의 개념에 있습니다.

```ts
function logLength<T extends { length: number }>(input: T): void {
	console.log(input.length);
}

logLength("Hello"); // 문자열은 length를 가지고 있으므로 OK
logLength([1, 2, 3]); // 배열도 length를 가지고 있으므로 OK
logLength({ length: 10 }); // 객체도 length를 가지고 있으므로 OK
```

다음과 같이 `<T extends { length: number }>` 를 사용하게 되면, 입력받는 input 의 key 값에 length 가 포함되어 있음을 완벽하게 보장합니다.

그렇다면 아래의 예시에서도 사용해보겠습니다.

```ts
type MyPick<T, K extends keyof T> = {
	[P in K]: T[P];
};
```

`K`는 `T`의 `key`중에 하나임을 보증합니다. 따라서 `K`(여기서 `K`는 `T`의 `key` 모임의 부분집합을 의미)
`[P in K]: T[P]`는 `K`에 있는 각 키 `P`에 대해 `T[P]`의 타입을 반환하는 새로운 객체 타입을 만듭니다.

### keyof

keyof는 객체 타입의 모든 키를 문자열 리터럴 유니언 타입으로 반환합니다. 예를 들어, 특정 인터페이스나 객체 타입이 있을 때, keyof는 해당 타입의 프로퍼티 이름들을 유니언 타입으로 반환합니다.

```ts
// 기본 객체 타입
interface Todo {
	title: string;
	description: string;
	completed: boolean;
}

type TodoKeys = keyof Todo;

// TodoKeys의 예상 출력 =>

type TodoKeys = "title" | "description" | "completed";

// --------------------------------------------------
// 객체 리터럴 타입
const person = {
	name: "John",
	age: 30,
	address: "123 Main St",
};

type PersonKeys = keyof typeof person;
// PersonKeys 예상출력
type PersonKeys = "name" | "age" | "address";

// --------------------------------------------------
// keyof와 인덱스 시그니처(Index Signature)
interface StringMap {
	[key: string]: string;
}

type StringMapKeys = keyof StringMap;
// StringMapKeys 의 예상출력
type StringMapKeys = string;

// --------------------------------------------------
// keyof와 인스턴스

class Point {
	x: number;
	y: number;

	constructor(x: number, y: number) {
		this.x = x;
		this.y = y;
	}

	move(dx: number, dy: number): void {
		this.x += dx;
		this.y += dy;
	}
}

type PointKeys = keyof Point;

// PointKeys의 예상 출력
type PointKeys = "x" | "y";
// 클래스의 인스턴스 프로퍼티인 x, y만 keyof의 결과에 포함됩니다. 메서드인 move는 keyof의 결과에 포함되지 않습니다.
```

#### Q. 왜 인스턴스에서 메서드들은keyof로 반환이 안되는걸까?

TypeScript에서 keyof 연산자가 객체 타입의 프로퍼티 키만을 반환하도록 설계되어 있기 때문입니다.  
TypeScript의 keyof 연산자는 특정 타입의 속성 이름만을 유니언 타입으로 반환합니다.  
이는 클래스의 인스턴스 메서드가 TypeScript에서 프로퍼티로 간주되지 않기 때문입니다.

```ts
type MethodKeys = keyof MyClass.prototype;
```

위와 같이 사용하게 되면, 메서드들의 함수명들을 가져올 수는 있습니다만, 좋은 방법은 아닙니다.

# Solution

```ts
/* _____________ Your Code Here _____________ */

type MyPick<T, K extends keyof T> = {
	[P in K]: T[P];
};
/* _____________ Test Cases _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Expected1, MyPick<Todo, "title">>>,
	Expect<Equal<Expected2, MyPick<Todo, "title" | "completed">>>,
	// @ts-expect-error
	MyPick<Todo, "title" | "completed" | "invalid">
];

interface Todo {
	title: string;
	description: string;
	completed: boolean;
}

interface Expected1 {
	title: string;
}

interface Expected2 {
	title: string;
	completed: boolean;
}
```
