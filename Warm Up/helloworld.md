# 문제 설명

Hello, World!

Type Challenges에서는 타입 단언(assertion)을 하기 위해 자체적인 타입 시스템을 사용합니다.

이 과제에서는, 아래의 코드를 변경해서 테스트 코드를 통과하세요. (타입 체크 에러 없음).

```ts
// string이 되어야 합니다.
type HelloWorld = any;
```

```ts
// 아래의 테스트가 통과하도록 만드세요.
type test = Expect<Equal<HelloWorld, string>>;
```

# Solution

```ts
type HelloWorld = string;
```

HelloWorld 의 타입을 string 으로 바꾸어 주기만 하면 아주 쉽게 해결할 수 있습니다🤗
