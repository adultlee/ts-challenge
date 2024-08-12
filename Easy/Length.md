# 문제 설명

배열(튜플)을 받아 길이를 반환하는 제네릭 Length<T>를 구현하세요.

예시:

```ts
type tesla = ["tesla", "model 3", "model X", "model Y"];
type spaceX = [
	"FALCON 9",
	"FALCON HEAVY",
	"DRAGON",
	"STARSHIP",
	"HUMAN SPACEFLIGHT"
];

type teslaLength = Length<tesla>; // expected 4
type spaceXLength = Length<spaceX>; // expected 5
```

# 풀이

```ts
type Length<T extends readonly any[]> = T["length"];
```

가장 쉽게 풀어 낼 수 있는 방법입니다.

```ts
type Length<T extends readonly any[]> = T extends { length: infer L }
	? L
	: never;
```

TypeScript의 조건부 타입과 infer 키워드를 사용하여 배열(또는 튜플)의 길이를 추론하는 방법입니다.

`T extends { length: infer L } > ...` 조건부 타입의 형태로 작성된 이 부분은 T가 { length: infer L }라는 구조와 `일치`하는지 확인합니다.
`{ length: infer L }`는 T 타입이 length라는 속성을 가지고 있는지를 확인하고, infer 키워드를 사용하여 그 속성의 타입을 `L로 추론(infer)`합니다.

그 후 추론된 결과를 반환합니다.

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type Length<T extends readonly any[]> = T["length"];

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

const tesla = ["tesla", "model 3", "model X", "model Y"] as const;
const spaceX = [
	"FALCON 9",
	"FALCON HEAVY",
	"DRAGON",
	"STARSHIP",
	"HUMAN SPACEFLIGHT",
] as const;

type cases = [
	Expect<Equal<Length<typeof tesla>, 4>>,
	Expect<Equal<Length<typeof spaceX>, 5>>,
	// @ts-expect-error
	Length<5>,
	// @ts-expect-error
	Length<"hello world">
];
```
