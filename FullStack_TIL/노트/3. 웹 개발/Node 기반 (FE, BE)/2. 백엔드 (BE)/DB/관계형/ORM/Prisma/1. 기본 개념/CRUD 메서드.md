## 함수 예시 보는 법

![[Pasted image 20250830230308.png]]

**prisma.모델이름.메서드** 로 사용.


## 1. Create 메서드 (post)

- create()
- createMany()
- data 절 - 리퀘스트 바디가 들어간다.

```js
app.post('/users', async (req, res) => {
  // 리퀘스트 바디 내용으로 유저 생성
  const user = await prisma.user.create({
    data: req.body,
  });
  
  res.status(201).send(user);
});
```

여러 개 생성도 가능

```js
await prisma.user.createMany({
  data: [
    { name: 'Alice', email: 'alice@example.com' },
    { name: 'Bob', email: 'bob@example.com' },
  ],
});
```

## 2. Read 메서드 (get)

- findMany() : 조건 조회
- findUnique() : 단일 조회
- findFirst() : 첫 번째 일치 항목 조회
- where 절 - 필터링 역할을 한다.
```js
app.get('/users/:id', async (req, res) => {
  const { id } = req.params;
  const user = await prisma.user.findUnique({
    where: { id },
  });
  res.send(user);
});
```

#### <쿼리 파라미터가 없으면 필터링에서 제외하는 방법>
```js
app.get('/users/:id', async (req, res) => {
  const { category } = req.params;
  const where = category ? { category }
  const user = await prisma.user.findUnique({
    where: { id },
  });
  res.send(user);
});
```

1. {} 객체는 필드 이름과 값이 같으면 생략이 가능하다
   { category: category } 처럼 안 써도 되는 이유.
2. 쿼리 파라미터가 없으면 필터링에서 제외하는 것도 가능

## 3. Update 메서드 (patch)

- update() : 단일 레코드 수정
- updateMany() : 다중 레코드 수정

```js
app.patch('/users/:id', async (req, res) => {
  const { id } = req.params;
  // 리퀘스트 바디 내용으로 id에 해당하는 유저 수정
  const user = await prisma.user.update({
    where: {id},
    data: req.body,
  });

  res.send(user);
});
```

## 4. Delete 메서드 (delete)

- delete() : 단일 레코드 수정
- deleteMany() : 다중 레코드 수정

```js
app.delete('/users/:id', async (req, res) => {
  const { id } = req.params;
  // id에 해당하는 유저 삭제
  await prisma.user.delete({
    where: {id},
  });
  res.sendStatus(204);
});
```

## 5. 기타 유용한 메서드
- **count**: 조건에 맞는 레코드 수
```js
  await prisma.user.count({
  where: { role: 'ADMIN' },
});
```
- **upsert**: 없으면 생성, 있으면 수정 (create, update를 합친 메서드)
```js
await prisma.user.upsert({
  where: { email: 'seungchan@example.com' },
  update: { name: 'Updated Seungchan' },
  create: { name: 'Seungchan', email: 'seungchan@example.com' },
});
```