CRUD 중 Read를 구현할 때 사용한다.

```jsx
const {
    data: todos,
    isPending,
    error,
  } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
    staleTime: 1000 * 60 * 5
  });
```

### 사용하는 이유

1) 기존에 try-catch-finally문으로 구현하던 pending, error 처리를,
	[[기존 pending, error 구현 방법 (useEffect 방식)]]
	useQuery 함수로 모두 해결 할 수 있다. 
	
2)  [[캐싱]]
	같은 데이터를 다시 받아오지 않는 시간을 설정하여,
	로딩 속도 효율을 높일 수 있다.
### 사용법

1) useQuery는 queryKey와 queryFn으로 이루어져 있다.
```
queryKey: 배열이 들어 간다. (key를 특정하는 역할)
queryFn: Promise를 반환하는 함수가 들어간다. (일반적으로 api 함수)
```

2) Provider 설정이 필요하다. (Context와 유사) (provider 주입 패턴)
```jsx
// src/app/providers.jsx
"use client";

import { QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

export default function Providers({ children }) {
  const [queryClient] = useState(() => new QueryClient());
  return (
    <QueryClientProvider client={queryClient}>
      <ReactQueryDevtools initialIsOpen={false} />
      {children}
    </QueryClientProvider>
  );
}

// src/app/layout.jsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
	      <Providers>
	        {children}
	      </Providers>
      </body>
    </html>
  );
}
```

 중요) [[Query 옵션 (enabled, select)]]
