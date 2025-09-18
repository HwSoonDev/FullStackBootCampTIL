```jsx
 //퀴리 메서드 체인. (쿼리 반환)
  const items = await Product
    .find(filter)//검색 쿼리
    .sort(sortOption)//정렬 쿼리
    .skip(offset)//오프셋 방식 페이지네이션
    .limit(count);//페이지네이션


  res.send(items);
```

```
모델
product

모델 메서드 (-> 쿼리를 반환)
.find()

쿼리 메서드 (-> 쿼리를 조작 / 정렬, 개수 제한, 페이지네이션)
.sort()
.skip()
.limit()
```

| 종류            |                                                                | 메서드                                               |
| ------------- | -------------------------------------------------------------- | ------------------------------------------------- |
| **모델(Model)** | 스키마(도큐먼트 틀)를 통해 만들어진 도큐먼트.<br>**모델 메서드**를 이용해서 각 요소에 접근할 수 있다. | find() - 쿼리 필터를 인자로 받는다.<br>[[쿼리필터]] <br>         |
| **쿼리(Query)** | 쿼리는 DB를 탐색해서 나온 값. **쿼리 메서드**를 이용해서 더 자세하게 탐색할 수 있다.           | .sort() - 정렬<br>.skip() - 오프셋<br>.limit() - 검색 개수 |


```js
app.get('/subscriptions', async (req, res) => {
  const sort = req.query.sort;
  const count  = Number(req.query.count) || 0;
  const sortOption = {
    createsAt: sort === 'oldest' ? 'asc' : 'desc'
  };

  const subs = await Subs.find().sort(sortOption).limit(count);//퀴리 메서드 체인이다. 모두 쿼리를 반환한다.
  res.send(subs);
});
```

- [Mongoose 공식 문서 - Model 메소드](https://mongoosejs.com/docs/queries.html)
- [Mongoose 공식 문서 - Query 메소드](https://mongoosejs.com/docs/api/query.html)