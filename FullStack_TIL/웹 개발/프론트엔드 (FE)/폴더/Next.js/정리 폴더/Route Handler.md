프론트에서 백엔드 구현하기.
api 요청 처리를 app 라우터 방식 주소 폴더에 구성하는 것.
route.ts(js)는 page.js와 함께 둘 수 없음.

물론이죠! Next.js의 **Route Handler**는 Next.js 13 이상에서 도입된 **App Router** 기능 중 하나로, 기존의 `pages/api` 방식 대신 `app` 디렉토리에서 API 경로를 정의할 수 있게 해줍니다. 서버 요청을 처리하는 방식이 더 깔끔하고 모듈화되어 있어요.

🚀 Route Handler란?

`route.ts` 또는 `route.js` 파일을 통해 특정 경로에 대한 HTTP 요청을 직접 처리할 수 있는 기능입니다. 예를 들어, `/api/hello` 경로에 대한 GET 요청을 처리하려면 다음과 같이 작성합니다:

// app/api/hello/route.ts import { NextResponse } from 'next/server' export async function GET() { return NextResponse.json({ message: '안녕하세요!' }) } export async function POST(request: Request) { const body = await request.json() return NextResponse.json({ 받은_데이터: body }) }

📁 파일 구조

- `app` 디렉토리 내부에 `route.ts` 파일을 생성합니다.
- 경로는 디렉토리 구조에 따라 자동으로 매핑됩니다.
- 예: `app/api/user/[id]/route.ts` → `/api/user/123` 같은 동적 라우팅도 가능

⚠️ `page.tsx`와 `route.ts`를 같은 디렉토리에 함께 두면 충돌이 발생하므로 주의하세요.

🔧 지원하는 HTTP 메서드

다음과 같은 메서드를 각각 함수로 정의할 수 있습니다:

- `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, `OPTIONS`

각 메서드는 독립적으로 관리되므로 코드가 더 깔끔하고 유지보수가 쉬워요.