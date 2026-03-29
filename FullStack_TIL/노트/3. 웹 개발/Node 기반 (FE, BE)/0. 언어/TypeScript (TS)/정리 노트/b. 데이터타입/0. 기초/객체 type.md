```ts
// 단순 객체 타입
type Person = {
  name: string;
  age: number;
  isStudent: boolean;
  email?: string; // 선택적 속성 (옵셔널)
};

const student: Person = {
  name: "Jane",
  age: 22,
  isStudent: true,
};

// 중첩 객체 타입 예시
type Address = {
  street: string;
  city: string;
  country: string;
};

// 객체 타입간 인터섹션은 모든 조건을 만족하는 새로운 객체 타입
type PersonWithAddress = Person & {
  address: Address;
};

const studentWithAddress: PersonWithAddress = {
  name: "John",
  age: 24,
  isStudent: false,
  address: {
    street: "123 Main St",
    city: "Anytown",
    country: "USA",
  },
};

// 인덱스 시그니처: 객체의 속성 이름을 동적으로 사용해야 할 때
type DynamicObject = {
  [key: string]: string | number; // 문자열 키에 대해 string 또는 number 값을 가질 수 있음
};

const dynamicData: DynamicObject = {
  name: "홍길동",
  age: 30,
  hobby: "독서",
  score: 95,
};
```