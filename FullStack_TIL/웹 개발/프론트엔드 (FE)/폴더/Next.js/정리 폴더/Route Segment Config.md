Next.js의 **Route Segment Config**는 `app` 디렉토리 기반 라우팅에서 각 경로 세그먼트의 동작을 세밀하게 제어할 수 있는 설정 방식이에요. 페이지, 레이아웃, 또는 API 핸들러(`route.ts`)에서 직접 export하는 변수들을 통해 캐싱, 렌더링 방식, 런타임 등을 조절할 수 있습니다.

🧩 주요 설정 옵션

| 옵션 이름              | 타입 / 값 범위                                                                | 설명                                  |
| ------------------ | ------------------------------------------------------------------------ | ----------------------------------- |
| **`dynamic`**      | `'auto'``'force-dynamic'``'force-static'``'error'`                       | 페이지/레이아웃의 동적 렌더링 여부 설정<br>**(SSG)** |
| **`revalidate`**   | `false``0``number`                                                       | ISR 방식으로 재생성 주기 설정                  |
| `dynamicParams`    | `true``false`                                                            | (ISG)                               |
| **`fetchCache`**   | `'auto'``'force-cache'``'force-no-store'``'only-cache'``'only-no-store'` | `fetch()` 요청의 캐시 정책 설정              |
| `runtime`          | `'nodejs'``'edge'`                                                       | 실행 환경 선택                            |
| `preferredRegion`  | `'auto'`, `'global'`, `'home'`, 또는 리전 문자열 배열                             | 서버리스 함수의 배포 리전 지정                   |
| `experimental_ppr` | `true``false`                                                            | Partial Prerendering 활성화 여부         |

📦 예시: `page.tsx`에서 설정

```js
// app/products/[slug]/page.tsx

export const dynamic = 'force-static'
export const revalidate = 60
export const dynamicParams = false
export const fetchCache = 'force-cache'

export default function ProductPage({ params }) {
  const { slug } = params
  // 페이지 내용...
}
```

- `dynamic: 'force-static'` → 완전 정적 렌더링
- `revalidate: 60` → 60초마다 백그라운드에서 재생성
- `dynamicParams: false` → `generateStaticParams`로 정의되지 않은 경로는 404 처리
- `fetchCache: 'force-cache'` → 모든 fetch 요청을 캐시

🧠 참고 사항

- `dynamicParams: false`를 설정하면 반드시 `generateStaticParams()`를 정의해야 합니다. 그렇지 않으면 해당 경로는 404를 반환해요.
- `route.ts`에서도 설정이 가능하지만 `page.tsx`와 같은 세그먼트에 함께 존재하면 충돌이 발생할 수 있어요.
- 이 설정들은 점차적으로 새로운 캐시 모델로 대체될 예정이며, 일부 옵션은 향후 deprecated될 수 있습니다.