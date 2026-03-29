#### tsconfig.json

TypeScript 컴파일 대상과 컴파일 옵션을 설정하는 파일

```json
{
  "compilerOptions": {
    // 컴파일 옵션들
    "target": "es2016",           // 어떤 JS 버전으로 컴파일할지
    "module": "commonjs",         // 어떤 모듈 시스템 사용할지
    "outDir": "./dist",           // 컴파일된 파일 저장 위치
    "rootDir": "./src",           // 소스 시작점
    "strict": true                // 엄격한 타입검사 활성화
  },
  
  // 컴파일 대상들
  "include": ["src/**/*"],        // 컴파일할 파일들
  "exclude": []                   // 컴파일에서 제외할 파일들
}
```

```
tip) 
typescript는 commonjs, es모듈 방식 둘 다로 번역될 수 있어,
프로젝트 환경에 제약도 덜 받는 장점도 있다
```

#### target, module 설정 차이

target과 module 설정에 따라 변환되어 나오는 js 파일이 문법이 달라진다.

```js
"target": "es2016",           // 어떤 JS 버전으로 컴파일할지
"module": "esnext", 
```


target (es 모듈 버전)
![[Pasted image 20251117131715.png]]

module (es모듈 or commonjs)
![[Pasted image 20251117131811.png]]