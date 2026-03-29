
## d.ts 파일이란?

- d.ts 파일(Declaration Files)은 TypeScript의 타입 선언만을 포함하는 파일입니다. 실제 구현 코드는 없고, 타입 정보만 제공합니다.

### 주요 용도

- JavaScript 라이브러리에 타입 정보 제공
- 전역 타입 선언
- 모듈의 타입 인터페이스 정의
- 외부 라이브러리와의 타입 호환성 확보

## 2. 패키지 타입 정의의 필요성

### JavaScript 라이브러리 사용시 문제점

```tsx
// JavaScript 라이브러리 사용시
import _ from 'lodash'; // 타입 정보 없음

// TypeScript가 타입을 추론할 수 없음
const result = _.lowerCase("HELLO WORLD");
```

### d.ts 파일로 해결

```tsx
// lodash.d.ts (간단한 예시)
declare module 'lodash' {
  interface LODASH {
    lowerCase: (param: string) => string;
  }
  const _: LODASH;
  export default _;
}

// 이제 타입 안전하게 사용 가능
import _ from 'lodash';
const result: number[] = _.lowerCase("HELLO WORLD"); // "hello world"
```

## 3. 타입 설치 방법

### @types 패키지 사용

🤩 사실 누군가 이미 lodash 패키지의 d.ts 파일을 만들어서 npm 에 등록해 두었습니다.

```bash
npm install @types/lodash
```

-> express.d.ts 등 라이브러리의 타입 정의가 필요한 경우 설치해서 사용하자!
### 타입이 내장된 패키지와 아닌 패키지를 구분 방법

npm registry 에 들어가서 패키지 검색하면 직관적으로 확인 가능합니다.

1. 패키지 설치 시 포함(빌트인)되어 있는 경우 npm 패키지명 우측에 `TS` 라고 표기되며, js파일 없이 모두 타입스크립트로 되어 있거나 d.ts파일도 함께 설치되니 신경쓰지 않아도 좋습니다.
![[Pasted image 20251117134815.png]]
![[Pasted image 20251117134823.png]]

---

## 4. namespace vs module

### namespace (네임스페이스)

전역 스코프에서 사용하는 타입 그룹화 방법입니다. 주로 전역 라이브러리나 레거시 코드에서 사용됩니다.

```tsx
// global-lib.d.ts
declare namespace MyLibrary {
  interface Config {
    apiUrl: string;
    timeout: number;
  }

  interface User {
    id: number;
    name: string;
  }

  function init(config: Config): void;
  function getUser(id: number): Promise<User>;

  namespace Utils {
    function formatDate(date: Date): string;
    function parseJson<T>(json: string): T;
  }
}

// 사용법 - 전역으로 접근 가능
// HTML 에서 CDN 방식으로 전역적으로 로드된 경우, 별도의 import 없음
// <script src="<https://cdn.example.com/my-library.js>"></script>
MyLibrary.init({ apiUrl: '<https://api.com>', timeout: 5000 });
const user = await MyLibrary.getUser(1);
const formatted = MyLibrary.Utils.formatDate(new Date());

```

### module (모듈)

ES6 모듈 시스템을 사용하는 현대적인 방법입니다. import/export를 통해 사용합니다.

```tsx
// modern-lib.d.ts
declare module 'modern-lib' {
  export interface Config {
    apiUrl: string;
    timeout: number;
  }

  export interface User {
    id: number;
    name: string;
  }

  export function init(config: Config): void;
  export function getUser(id: number): Promise<User>;
	
	// 모듈 내부에서의 함수그룹화에서는 namespace 를 여전히 사용 (이 경우는 레거시 아님)
  export namespace Utils {
    function formatDate(date: Date): string;
    function parseJson<T>(json: string): T;
  }

// default export
  export default class ApiClient {
    constructor(config: Config);
  }
}

// 사용법 - import 필요
import ApiClient, { init, getUser, Utils } from 'modern-lib';

init({ apiUrl: '<https://api.com>', timeout: 5000 });
const user = await getUser(1);
const formatted = Utils.formatDate(new Date());
const client = new ApiClient({ apiUrl: '<https://api.com>', timeout: 5000 });
```

### 주요 차이점 요약

|특징|namespace|module|
|---|---|---|
|스코프|전역|모듈 스코프|
|사용법|직접 접근|import/export|
|현대성|레거시|현대적|
|네이밍 충돌|가능성 높음|격리됨|

## 권장하는 d.ts 사용 범위

### ✅ d.ts 파일이 적절한 경우

```tsx
// 1. 모듈 타입 선언 (외부 라이브러리)
declare module "some-js-library" {
  export function doSomething(): void;
}

// 2. 전역 환경 확장
declare global {
  interface Window {
    kakao: any;
  }
}

// 3. 정적 파일 타입
declare module "*.svg" {
  const content: string;
  export default content;
}

declare module "*.png" {
  const src: string;
  export default src;
}

```

- kakaomap 을 CDN 형식으로 불러올 때 Window 객체 타입 확장 필요
    
    ```tsx
    <!doctype html>
    <html>
      <head>
        <meta charset="utf-8" />
        <title>Kakao 지도 시작하기</title>
      </head>
      <body>
        <div id="map" style="width:500px;height:400px;"></div>
        <script
          type="text/javascript"
          src="//dapi.kakao.com/v2/maps/sdk.js?appkey=발급받은 APP KEY를 넣으시면 됩니다."
        ></script>
        <script>
          var container = document.getElementById('map');
          var options = {
            center: new kakao.maps.LatLng(33.450701, 126.570667),
            level: 3,
          };
     
          var map = new kakao.maps.Map(container, options);
        </script>
      </body>
    </html>
    ```
    

### ❌ d.ts 대신 .ts 파일 권장

```tsx
// types/user.ts - 일반 타입 정의
export interface User {
  id: number;
  name: string;
  email: string;
}

export type UserRole = 'admin' | 'user' | 'guest';

export interface ApiResponse<T> {
  data: T;
  status: number;
  message?: string;
}

// types/api.ts - API 관련 타입
export class ApiClient {
  constructor(private baseUrl: string) {}
  async get<T>(url: string): Promise<T> {
// 실제 구현
  }
}

export const API_ENDPOINTS = {
  USERS: '/users',
  POSTS: '/posts'
} as const;
```