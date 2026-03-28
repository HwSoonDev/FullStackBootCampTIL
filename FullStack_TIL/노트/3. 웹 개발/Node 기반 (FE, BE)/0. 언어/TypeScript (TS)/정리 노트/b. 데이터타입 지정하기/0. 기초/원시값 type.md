### 원시값
```ts
{
  const name: string = "John"; // 문자열 타입
  const age: number = 25; // 숫자 타입
  const isStudent: boolean = true; // 불리언 타입
  const nothing: null = null; // null 타입
  const notDefined: undefined = undefined; // undefined 타입

  const anyValue: any = "Hello"; // any 타입, 어떤 타입이든 될 수 있음
  const unknownValue: unknown = 42;
  // unknown 타입, 어떤 타입이든 될 수 있지만 런타임에 타입 검사해야 사용할 수 있음.
  if (typeof unknownValue === "number") {
    console.log(unknownValue + 1);
  }

  // 리터럴 타입 : 특정값만을 허용하는 타입을 의미합니다.
  const dataOnly10: 10 = 10;

  // 유니온 타입: 여러 타입 중 하나를 허용하는 타입
  let direction: "left" | "right" | "up" | "down";
  direction = "left";

  // 인터섹션 타입: 모든 타입 조건을 동시에 만족해야하는 타입
  type RedOrBlue = "red" | "blue";
  type BlueOrGreen = "blue" | "green";
  type OnlyBlue = RedOrBlue & BlueOrGreen;
  let blue: OnlyBlue = "blue";

  // enum 타입: 열거형으로, 관련된 상수들을 하나의 네임스페이스로 묶어서 관리
  enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
  }
  let playerDirection: Direction = Direction.Up;
}
```