### VITE 설치 하기

1) 프로젝트 폴더에 들어가서 vscode로 열기
2) Vscoe 터미널에서 다음과 같이 입력
```
npm create vite@latest . -- --template react 
```
3) 폴더 안에 vite React 프로젝트가 자동 생성된다. 
4) 서버 실행 명령어
```
npm run dev
```

[[VITE 설치 가이드 (코드잇)]]

 <create vite 커맨드 입력 예시>

```
C:\Users\vb120\Desktop\codeit_react>npm -v
10.9.2
C:\Users\vb120\Desktop\codeit_react>node -v
v22.17.1
C:\Users\vb120\Desktop\codeit_react>npm create vite@latest . -- --template react
Need to install the following packages:
create-vite@7.0.3
Ok to proceed? (y) y
> npx
> create-vite . --template react
│
◇  Scaffolding project in C:\Users\vb120\Desktop\codeit_react...
│
└  Done. Now run:
  npm install
  npm run dev
```


**[오류 해결 모음]**

**1. vscode 터미널에서 npm, node 명령어가 안될 때,**

1) file > preference > settings >> ‘widow profile’ 검색 > Terminal 프로파일 cmd로 설정

2) 터미널 +버튼 눌러서 cmd 창으로 열기 (기본은 PowerShell)

è PowerShell에서는 node, npm 명령어가 안되는 듯 하다.


**2. Vite가 인식이 안될 때 (npm run dev 오류)**

pakage.json의 “script”에 분명 “dev”가 있는데, npm run dev가 안된다.

npm install vite --safe-dev 로 다시 vite를 깔아줬다.

'vite'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는

배치 파일이 아닙니다.

```
C:\Users\vb120\Desktop\codeit_react>npm install vite --save-dev
added 152 packages, and audited 153 packages in 12s
33 packages are looking for funding
  run `npm fund` for details
found 0 vulnerabilities
C:\Users\vb120\Desktop\codeit_react>npm run dev
> codeit_react@0.0.0 dev
> vite
  VITE v7.0.6  ready in 298 ms
  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

