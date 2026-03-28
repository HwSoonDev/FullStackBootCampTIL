supabase에 바로 접근하는 것이 아니라,
백 서버를 따로 만들어서 엔드포인트를 한번더 거치게 할 때,
프론트에서  사전에 발급된 access token을 사용하는 경우, 사용하는 방식.

```js
//인증, 인가가 필요한 데이터 접근
app.get('/api/protected-data', async (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  const supabaseWithAuth = createClient(SUPABASE_URL, SUPABASE_ANON_KEY, {
    global: { headers: { Authorization: `Bearer ${token}` } }
  });
  const { data, error } = await supabaseWithAuth.from('Test').select('*');
  res.json({ data, error });
});
```

원래 프론트에서 바로 supabase 라이브러리를 사용한다면?

  
```js
import { createClient } from '@supabase/supabase-js';
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);
// 인증 토큰 설정
supabase.auth.setAuth(access_token);
```

  
이 형태로 인증 토큰을 설정해서 간편하게 사용하지만,
여기서는 api 엔드포인트 안에서 인증, 인가 여부를 판단해야 해서
엔드포인트 별로 `클라이언트 객체`를 따로 만들어서 사용한다.