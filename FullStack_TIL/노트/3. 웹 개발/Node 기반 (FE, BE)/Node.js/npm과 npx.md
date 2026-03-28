### npm과 npx의 차이

둘 다 Node.js와 함께 쓰이지만 역할이 달라요.

- **npm (Node Package Manager)**  
  - **패키지(라이브러리) 관리 도구** + 레지스트리
  - 하는 일:
    - 패키지 설치/삭제:  
      - `npm install express` (로컬 설치)  
      - `npm install -g typescript` (글로벌 설치)
    - 프로젝트 스크립트 실행:  
      - `npm run dev`, `npm run build` (→ `package.json` 의 `scripts` 실행)
  - 요약: **“패키지를 관리하고, 프로젝트 스크립트를 실행”**하는 도구

- **npx (Node Package eXecute)**  
  - **패키지를 “실행”하는 도구**
  - 하는 일:
    - **글로벌 설치 없이** 특정 커맨드를 바로 실행:
      - `npx prisma init` → `prisma` 패키지를 (필요 시) 받아서 한 번 실행
      - `npx create-react-app my-app` 등
    - `node_modules/.bin` 안에 있는 실행 파일을 편하게 호출
  - 요약: **“설치 여부 신경 안 쓰고, 패키지의 실행 파일을 바로 돌리는”** 도구
