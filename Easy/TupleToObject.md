# 문제 설명

배열(튜플)을 받아, 각 원소의 값을 key/value로 갖는 오브젝트 타입을 반환하는 타입을 구현하세요.

예시:

```ts
const tuple = ["tesla", "model 3", "model X", "model Y"] as const;

type result = TupleToObject<typeof tuple>; // expected { 'tesla': 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

# 풀이

입력받아서 사용할 tuple, tupleNumber, tupleSymbol, tupleMix를 바탕으로 TupleToObject의 제네릭을 제한합니다.
`T extends readonly (string | number | symbol)[]`

제한시킨 이후 해당 값을 바탕으로 value = key 가 되도록 변경합니다.

`[P in T[number]]: P;`

# Solution

```ts
/* _____________ 여기에 코드 입력 _____________ */

type TupleToObject<T extends readonly (string | number | symbol)[]> = {
	[P in T[number]]: P;
};

/* _____________ 테스트 케이스 _____________ */
import type { Equal, Expect } from "@type-challenges/utils";

const tuple = ["tesla", "model 3", "model X", "model Y"] as const;
const tupleNumber = [1, 2, 3, 4] as const;
const sym1 = Symbol(1);
const sym2 = Symbol(2);
const tupleSymbol = [sym1, sym2] as const;
const tupleMix = [1, "2", 3, "4", sym1] as const;

type cases = [
	Expect<
		Equal<
			TupleToObject<typeof tuple>,
			{
				tesla: "tesla";
				"model 3": "model 3";
				"model X": "model X";
				"model Y": "model Y";
			}
		>
	>,
	Expect<Equal<TupleToObject<typeof tupleNumber>, { 1: 1; 2: 2; 3: 3; 4: 4 }>>,
	Expect<
		Equal<
			TupleToObject<typeof tupleSymbol>,
			{ [sym1]: typeof sym1; [sym2]: typeof sym2 }
		>
	>,
	Expect<
		Equal<
			TupleToObject<typeof tupleMix>,
			{ 1: 1; "2": "2"; 3: 3; "4": "4"; [sym1]: typeof sym1 }
		>
	>
];
```
