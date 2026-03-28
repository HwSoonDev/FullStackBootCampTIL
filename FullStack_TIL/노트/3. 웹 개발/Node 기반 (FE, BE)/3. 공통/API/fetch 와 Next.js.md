🔑 Next.js의 `fetch` 자동 최적화 특징

1. **중복 요청 제거 (Deduplication)**

- 동일한 URL과 옵션으로 여러 번 `fetch`를 호출하면 Next.js가 자동으로 **한 번만 요청**을 보내고 결과를 공유합니다.
- 예: 여러 컴포넌트에서 같은 API를 호출해도 네트워크 요청은 한 번만 발생.

2. **병렬 처리 (Parallelization)**

- 서버 컴포넌트에서 여러 `fetch`를 순차적으로 호출하면 성능이 떨어집니다.
- Next.js는 가능한 경우 자동으로 **병렬 실행**하여 전체 응답 시간을 줄입니다.

3. **캐싱 (Caching)**

- `fetch` 응답은 기본적으로 캐싱되지 않지만, 옵션을 통해 제어할 수 있습니다:

- `cache: 'force-cache'` → 빌드 시점에 캐싱, 정적 페이지에 적합.
- `cache: 'no-store'` → 항상 최신 데이터 요청.
- `next: { revalidate: 10 }` → ISR(Incremental Static Regeneration)처럼 일정 주기마다 재검증.

4. **서버 컴포넌트 최적화**

- `fetch`는 서버 컴포넌트에서 실행될 때 **React 18의 Suspense**와 결합되어, 데이터가 준비되면 스트리밍으로 렌더링됩니다.
- 덕분에 초기 로딩 속도가 빨라지고, 부분적으로 UI를 먼저 보여줄 수 있습니다.

5. **메타데이터와 연동**

- `generateMetadata` 같은 함수에서도 `fetch`를 사용할 수 있으며, Next.js가 자동으로 최적화해 불필요한 중복 호출을 줄여줍니다.

📊 비교: 일반 fetch vs Next.js fetch

![[Pasted image 20251215151755.png]]