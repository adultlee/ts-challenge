# 문제 설명

내장 타입인 Readonly<T> 제네릭을 직접 구현해보세요.

T의 모든 프로퍼티가 readonly로 설정된 타입을 만들어야 합니다. 이는 생성된 타 입의 프로퍼티들이 재할당 될 수 없음을 의미합니다.

예시:

```ts
interface Todo {
	title: string;
	description: string;
}

const todo: MyReadonly<Todo> = {
	title: "Hey",
	description: "foobar",
};

todo.title = "Hello"; // Error: cannot reassign a readonly property
todo.description = "barFoo"; // Error: cannot reassign a readonly property
```

# 풀이

`Readonly<T>`는 `TypeScript`의 내장 제네릭 중 하나로, 주어진 타입 `T`의 모든 프로퍼티를 읽기 전용(immutable)으로 만드는 역할을 합니다.

여기서 Readonly 기능을 수행하는 MyReadonly를 개발하면 됩니다.
이때 MyReadonly 을 immutable 하게 만들기 위해서는 readonly 키워드와 키-쌍 매핑기능을 한번더 활용합니다.

### readonly

```ts
const obj = { prop: 10 };
obj.prop = 20; // 정상 작동: 'prop'은 변경 가능
obj = { prop: 30 }; // 오류: 'obj' 자체는 const로 선언되어 재할당 불가

interface ReadOnlyObj {
	readonly prop: number;
}

const roObj: ReadOnlyObj = { prop: 10 };
roObj.prop = 20; // 오류: 'prop'은 readonly로 선언되어 변경 불가
```

### object.freeze 와는 어떤 차이가 있을까?

readonly 키워드는 TypeScript의 타입 시스템에 속하는 기능으로, 배열 또는 객체의 요소를 변경할 수 없도록 타입 수준에서 제약을 가합니다. 이는 `컴파일` 타임에 동작하며, `런타임`에서는 영향을 미치지 않습니다.

Object.freeze는 JavaScript의 내장 메서드로, 객체(또는 배열)를 얼려서 수정할 수 없게 만듭니다. 이는 런타임에도 동작하며, 객체의 모든 프로퍼티 또는 배열의 요소들을 수정, 추가, 삭제할 수 없도록 합니다.

```ts
const myArray = Object.freeze([1, 2, 3]);

myArray.push(4); // 오류: 'push'는 정의되지 않았습니다.
myArray[0] = 10; // 오류: '0'에 대한 값을 할당할 수 없습니다.
```

### 여전히 얕은 단계에서만 불변성이 제공된다. 깊게는 어떻게 할 수 있을까?

TypeScript의 readonly 키워드는 주로 객체나 배열의 1차적인 불변성을 보장해주는 역할을 합니다. 그러나 readonly는 기본적으로 얕은 불변성(shallow immutability)만 제공하므로, 객체의 중첩된 프로퍼티까지 불변으로 만들지는 않습니다. 따라서 깊은 불변성을 readonly만으로 구현하기 위해서는 TypeScript의 타입 시스템을 활용하여 `재귀적`으로 모든 중첩된 객체를 readonly로 만드는 방법을 사용해야 합니다.

```ts
type DeepReadonly<T> = {
	readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};
```

readonly를 사용해서, 재귀적으로 해당 T 제네릭이 object로서인 경우에만 해당하여 해당 객체를 재귀적으로 type 을 정의해나갑니다.

### 하지만 여전히 존재하는 문제

특정 타입 처리: 함수나 Date, Map, Set 등과 같은 특수한 객체 타입은 제대로 처리되지 않을 수 있습니다. 이를 보완하려면 추가적인 조건부 타입을 활용하거나 커스텀 타입을 정의할 수 있습니다.

# solution

```ts
/* _____________ Your Code Here _____________ */

type MyReadonly<T> = {
	readonly [P in keyof T]: T[P];
};

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [Expect<Equal<MyReadonly<Todo1>, Readonly<Todo1>>>];

interface Todo1 {
	title: string;
	description: string;
	completed: boolean;
	meta: {
		author: string;
	};
}
```
