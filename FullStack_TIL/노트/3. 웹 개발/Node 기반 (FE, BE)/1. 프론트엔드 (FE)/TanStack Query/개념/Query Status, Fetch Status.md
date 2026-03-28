
# Query Status

query status는 세 가지 상태 값을 가지는데요. query status는 `'pending'`, `'success'`, `'error'`의 상태값 중 하나를 가지게 됩니다. `'pending'`은 아직 데이터를 받아오지 못했을 때를 의미하고, `'error'`는 데이터를 받아오는 중에 에러가 발생했음을 뜻합니다. 그리고 데이터를 성공적으로 받아오게 되면 `'success'` 값을 가지게 됩니다. 이 상태 값들은 각각 `isPending`, `isError`, `isSuccess`와 매칭이 되는데요. 이 값들을 이용해 현재 쿼리의 상태가 어떤지 확인할 수 있습니다.

각각의 상태를 직접 확인해 볼까요. `useQuery()`의 결괏값을 콘솔에 찍어보면 결괏값이 두 번 출력이 되는데요. 이 중 첫 번째 결괏값을 살펴보면 `status`가 `'pending'` 상태임을 볼 수 있습니다.

![jkwlv00r8-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=7063&version=&directory=jkwlv00r8-image.png&name=jkwlv00r8-image.png)

가장 처음 컴포넌트가 마운트되고(DOM 트리에 추가되고) `useQuery()`가 실행되면서, 데이터를 아직 받아오기 전이므로 `'pending'` 상태가 되는 것이죠. 그 후에 찍힌 두 번째 결괏값을 살펴보면 `status`가 `'success'`로 되어 있고, `data` 항목에서 실제 데이터들도 확인할 수 있습니다.

![0d6xe3vjm-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=7063&version=&directory=0d6xe3vjm-image.png&name=0d6xe3vjm-image.png)

데이터를 받아 오는 중에 에러가 발생하면 status가 `'error'`가 될 텐데요. 예를 들어 API 주소를 없는 주소로 바꿔 보거나, 혹은 쿼리 함수에서 일부러 에러를 만들어 throw해 보면 `status`가 `'error'`로 바뀌고, `useQuery()` 결괏값의 `error` 값에서 그 에러를 살펴볼 수 있습니다.

src/api.js

``export async function getPosts() {   const response = await fetch(`${BASE_URL}/posts`);   const body = await response.json();    // 일부러 에러 발생   throw new Error('An error happened.');   return body; }``

![svqluhapw-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=7063&version=&directory=svqluhapw-image.png&name=svqluhapw-image.png)

# Fetch Status

그럼 이번엔 fetch status에 대해 알아보겠습니다. fetch status도 총 세 가지 상태 값을 가지는데요. 바로 `'fetching'`, `'paused'`, `'idle'`입니다. 우리가 `useQuery()`를 사용할 때 쿼리 함수라는 걸 `queryFn`으로 등록해 줬는데요. 이 쿼리 함수의 실행 상태를 말해주는 값이 바로 fetch status입니다. 현재 쿼리 함수가 실행되는 중일 때에는 `'fetching'` 상태가 됩니다. 만약 쿼리 함수가 시작은 했는데 실제로 실행되고 있지는 않다면 `'paused'` 상태가 되는데요. 대표적으로 네트워크가 오프라인이 된 경우 기본적으로 fetch status가 `'paused'` 상태가 됩니다. 마지막으로 쿼리 함수가 어떤 작업도 하고 있지 않은 상황, 즉 `'fetching'` 상태도 아니고 `'paused'` 상태도 아니라면 `'idle'` 상태가 됩니다.

각각의 status가 어떤 흐름으로 가게 되는지 다이어그램을 통해 좀 더 명확하게 알아봅시다.

![7d0b9ecds-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=7063&version=&directory=7d0b9ecds-image.png&name=7d0b9ecds-image.png)

먼저 처음으로 컴포넌트가 마운트되어 `useQuery()`가 실행되면, 데이터를 아직 받아오지 못했기 때문에 query status는 `'pending'`이 됩니다. 그리고 쿼리 함수가 실행되면서 fetch status는 `'fetching'` 상태가 되죠.

만약 네트워크 상태가 오프라인일 때 쿼리 함수가 실행된다면, fetch status는 `'paused'` 상태로 가게 됩니다.

데이터를 성공적으로 받았다면 query status는 `'success'`가 되고, 만약 데이터를 받아오는 과정에서 에러가 발생했다면 `'error'` 상태가 됩니다.

fetch status는 데이터를 성공적으로 가져왔는지 여부에 상관없이, 쿼리 함수의 실행이 끝나면 `'idle'` 상태가 되고요. 그 후에 데이터를 서버에서 다시 받아오는 refetch 작업이 발생하면 쿼리 함수가 재실행되면서 다시 `'fetching'` 상태로 가게 됩니다.

# 정리

이처럼 query status와 fetch status는 엄연히 독립적인 상태이기 때문에, 상황에 따라 query status와 fetch status가 다양한 조합의 형태로 나타날 수 있습니다. 이상적인 상황에서는 "pending & fetching" 상태에서 "success & idle" 상태가 되겠지만, 에러가 발생하는 경우 "error & idle" 상태가 될 수도 있고요. "success & idle" 상태에서 데이터를 refetch하게 되면 "success & fetching" 상태가 되기도 합니다. 이처럼 query status와 fetch status 값을 잘 활용하면, 다양한 상황에 맞춰 디테일한 구현을 할 수 있습니다.