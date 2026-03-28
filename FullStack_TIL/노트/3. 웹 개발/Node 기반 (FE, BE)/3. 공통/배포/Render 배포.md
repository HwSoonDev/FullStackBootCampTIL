## 가입
![[Pasted image 20250822184311.png]]
## 0. 정적 파일 서빙 설정

### a. 빌드 시 파일 생성 경로 설정

vite.config.js
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  build: {
    outDir: '../backend/public' // 백엔드에서 서빙할 위치로 지정
  }
})
```

이렇게 설정하면, 
빌드 시  `backend/public` 아래에 빌드 파일(`indec.html, assets/`)이 생성된다.

![[Pasted image 20250823135708.png]]

### b. 주의 사항

이미지 파일은 vite 빌드시 import 설정이 없으면 asset에 추가해주지 않는다!!
사용하는 이미지는 꼭 import 후 써주자.
```jsx
//이미지 파일은 꼭 import해서 써야 빌드 시 포함 시켜 준다!!
import facebookLogo from "images/social/facebook-logo.svg";
import twitterLogo from "images/social/twitter-logo.svg";
import youtubeLogo from "images/social/youtube-logo.svg";
import instagramLogo from "images/social/instagram-logo.svg";
```

## 1. 서비스 생성 (Wed service)

### a. 빌드/스타트 명령어 설정
```json
"scripts": {
    "start": "node backend/app.js",
    "build": "npm install && cd frontend && vite build",
    "dev": "concurrently \"npm run client\" \"npm run server\"",
    "client": "cd frontend && vite",
    "server": "cd backend && nodemon app.js",
    "seed": "node backend/data/seed.js"
  },
```

![[Pasted image 20250823124915.png]]


### b. 환경변수 설정 (DB URL, node 버전)
![[Pasted image 20250823124836.png]]

### c. 자동 배포 설정 - (git push 마다 자동 배포)
![[Pasted image 20250823125142.png]]

### d. 배포 로그
```bash
2025-08-23T03:54:46.160395659Z ==> Cloning from https://github.com/HwSoonDev/PandaMarket
2025-08-23T03:54:51.663701998Z ==> Checking out commit be17ce8b9031f4f9eb8d97dc5758b9c95f35c3e9 in branch react-황승찬-sprint6
2025-08-23T03:54:54.282712271Z ==> Using Node.js version 22.17.1 via environment variable NODE_VERSION
2025-08-23T03:54:54.308056165Z ==> Docs on specifying a Node.js version: https://render.com/docs/node-version
2025-08-23T03:54:56.268710249Z ==> Running build command 'npm run build'...
2025-08-23T03:54:56.381972887Z 
2025-08-23T03:54:56.381998717Z > codeit_react@0.0.0 build
2025-08-23T03:54:56.382003717Z > npm install && cd frontend && vite build
2025-08-23T03:54:56.382007608Z 
2025-08-23T03:54:59.646270299Z 
2025-08-23T03:54:59.646293109Z added 287 packages, and audited 288 packages in 3s
2025-08-23T03:54:59.64630648Z 
2025-08-23T03:54:59.64631097Z 57 packages are looking for funding
2025-08-23T03:54:59.64633573Z   run `npm fund` for details
2025-08-23T03:54:59.647289714Z 
2025-08-23T03:54:59.647305754Z found 0 vulnerabilities
2025-08-23T03:54:59.899850509Z vite v7.0.6 building for production...
2025-08-23T03:54:59.900656249Z 
2025-08-23T03:54:59.900671019Z (!) outDir /opt/render/project/src/backend/public is not inside project root and will not be emptied.
2025-08-23T03:54:59.900676309Z Use --emptyOutDir to override.
2025-08-23T03:54:59.90067995Z 
2025-08-23T03:54:59.956429271Z transforming...
2025-08-23T03:55:00.911214319Z ✓ 60 modules transformed.
2025-08-23T03:55:01.080078615Z rendering chunks...
2025-08-23T03:55:01.087227508Z computing gzip size...
2025-08-23T03:55:01.096345469Z ../backend/public/index.html                                 0.56 kB │ gzip:  0.40 kB
2025-08-23T03:55:01.096455862Z ../backend/public/assets/logo-i5whe4wr.svg                   5.57 kB │ gzip:  2.14 kB
2025-08-23T03:55:01.096566235Z ../backend/public/assets/favicon-CTAV_6uM.ico               15.41 kB
2025-08-23T03:55:01.096636977Z ../backend/public/assets/hero-image-IS5RVAL5.png            71.40 kB
2025-08-23T03:55:01.096674047Z ../backend/public/assets/bottom-banner-image-eVVlbA9m.png   72.09 kB
2025-08-23T03:55:01.096697238Z ../backend/public/assets/index-B2oxGZgm.css                 11.77 kB │ gzip:  2.80 kB
2025-08-23T03:55:01.096720219Z ../backend/public/assets/index-I_CnaqVI.js                 270.02 kB │ gzip: 86.99 kB
2025-08-23T03:55:01.097017126Z ✓ built in 1.17s
2025-08-23T03:55:02.361145696Z ==> Uploading build...
2025-08-23T03:55:10.934447074Z ==> Uploaded in 6.9s. Compression took 1.7s
2025-08-23T03:55:10.978297668Z ==> Build successful 🎉
2025-08-23T03:55:15.935307208Z ==> Deploying...
2025-08-23T03:55:35.34771495Z ==> Running 'npm run start'
2025-08-23T03:55:36.454270885Z 
2025-08-23T03:55:36.454305886Z > codeit_react@0.0.0 start
2025-08-23T03:55:36.454309966Z > node backend/app.js
2025-08-23T03:55:36.454312196Z 
2025-08-23T03:55:40.051537296Z [dotenv@17.2.1] injecting env (0) from .env -- tip: 📡 observe env with Radar: https://dotenvx.com/radar
2025-08-23T03:55:40.245803251Z Server Started
2025-08-23T03:55:41.231299041Z Connected to DB
2025-08-23T03:55:46.568253035Z ==> Your service is live 🎉
2025-08-23T03:55:46.747423411Z ==> 
2025-08-23T03:55:46.921563018Z ==> ///////////////////////////////////////////////////////////
2025-08-23T03:55:47.095620875Z ==> 
2025-08-23T03:55:47.276998311Z ==> Available at your primary URL https://pandamarket-kwxe.onrender.com
2025-08-23T03:55:47.276998311Z ==> Available at your primary URL https://pandamarket-kwxe.onrender.com
2025-08-23T03:55:47.451925448Z ==> 
2025-08-23T03:55:47.627493684Z ==> ///////////////////////////////////////////////////////////
```


## 2. URL 설정
| url 설정        |                                        |
| ------------- | -------------------------------------- |
| a. 프론트 URL    | **CORS 설정**: 백엔드에서 프론트 접근 허용           |
| b. 백 URL      | **프론트 api** 에서 필요                      |
| c. DB(원격) URL | process.env 설정 (환경 변수: Render 배포 시 설정) |

각 url을 배포 후 제공되는 url로 설정해줘야 서로 연결이 된다. (사이트가 정상 작동 한다.)

