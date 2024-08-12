# 문제설명

`Array.unshift`의 타입 버전을 구현하세요.

```typescript
type Result = Unshift<[1, 2], 0>; // [0, 1, 2]
```

# 풀이

크게 어려운 문제는 아니었습니다. 입력받은 U를 배열의 가장 앞순서에 배치하여 새로운 array 타입을 생성합니다.

### Union 타입의 장점

사실 여담이지만 다음과 같이 배열로 선언한 타입은 활용도가 높지 않습니다.  
유니언 타입은 배열보다 간단하고 명확합니다. 타입을 읽을 때, 배열보다 유니언 타입이 더 직관적이고 이해하기 쉽습니다.  
유니언 타입은 TypeScript의 타입 시스템과 자연스럽게 통합됩니다.  
예를 들어, 유니언 타입은 함수의 매개변수나 반환 값의 타입으로 쉽게 사용할 수 있습니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Unshift<T extends any[], U> = [U, ...T];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

type cases = [
	Expect<Equal<Unshift<[], 1>, [1]>>,
	Expect<Equal<Unshift<[1, 2], 0>, [0, 1, 2]>>,
	Expect<Equal<Unshift<["1", 2, "3"], boolean>, [boolean, "1", 2, "3"]>>
];
```
