## 0. 시딩

[[시드 데이터 넣기 (시딩)]]

## 1. 스키마 정의

'스키마(Schema)': 데이터(도큐먼트)의 틀.
'모델(Model)': 스키마를 기반으로 데이터를 생성하고 조회하고 수정, 삭제할 수 있는 인터페이스.
'쿼리(Query)': 모델 메서드로 DB를 검색한 결과 값.

Mongoose로 어떤 데이터를 다루려면 항상 스키마와 모델을 가장 먼저 정의해야 한다.

[[스키마 정의]]

## 2. Model 메서드 & Query 메서드
### Model
스키마(도큐먼트 틀)를 통해 만들어진 도큐먼트.
모델 메서드를 이용해서 각 요소에 접근할 수 있다.
Model 메서드로 검색하면 Query를 돌려 준다.
### Query
Model 메서드로 검색하면 Query를 돌려 준다.
쿼리는 DB를 탐색해서 나온 값. 쿼리 메서드를 이용해서 더 자세하게 탐색할 수도 있다.

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


## 3. CRUD 연산

[[CRUD 연산 (mogoose 리퀘스트)]]


## 4. 에러처리

[[Mongoose 에러처리]]