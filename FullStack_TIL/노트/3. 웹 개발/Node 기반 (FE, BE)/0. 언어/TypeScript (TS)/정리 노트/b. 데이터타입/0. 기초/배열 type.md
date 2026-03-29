```ts
// 숫자 타입 배열
const numbers: number[] = [1, 2, 3, 4, 5];

// 문자열 타입 배열
const names: string[] = ["Alice", "Bob", "Charlie"];

// 불리언 타입 배열
const bools: boolean[] = [true, false, true];

// 튜플(tuple) 타입 배열: 각 요소에 대해 정확한 타입과 순서를 지정 (요소 개수 제한됨)
const tupleArr: [string, number] = ["123", 123];

// 유니온(union) 타입 배열: 각 요소가 여러 가지 타입 중 하나일 수 있음을 나타낼 수 있습니다.
const unionArr: (string | number)[] = [123, "123", 123, 123, "123"];
	
```