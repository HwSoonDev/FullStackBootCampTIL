## GET (Read)
```js
app.get('/subscriptions', async (req, res) => {
  const sort = req.query.sort;
  const count  = Number(req.query.count) || 0;
  const sortOption = sort === 'price'
    ? {price : 'desc'}
    : {createdAt: 'desc'};
  
  const subs = await Subs.find().sort(sortOption).limit(count);//퀴리 메서드 체인이다. 모두 쿼리를 반환한다.
  res.send(subs);
});

app.get('/subscriptions/:id', async (req, res) => {
    const sub = await Subs.findById(req.params.id);//문자형으로 받기 때문에 숫자형변환 안한다.
  if(sub){
    res.send(sub);
  } else{
    res.status(404).send({message : 'Cannot find given id.'});
  }
});
```

## POST (Create)
```js
app.post('/subscriptions', async (req, res) => {
  const newSubscription = await Subscription.create(req.body);
  res.status(201).send(newSubscription);
});
```

mongoose를 사용하면 post는 엄청 편해진다.
모델 메서드 create만 사용하면 바디를 바로 보내서 올릴 수 있다.


## PATCH (Update)
```js
app.patch('/subscriptions/:id', asyncHandler(async (req, res) => {
  //양식 틀은 get과 비슷해서 복사 후 수정
  const id = req.params.id;
  const sub = await Subs.findById(id);
  if(sub){
    Object.keys(req.body).forEach((key)=>{
        sub[key] = req.body[key];
    });
    await sub.save(); //수정한 model을 MongoDB에 저장
    res.send(sub);
  } else{
    res.status(404).send({message : 'Cannot find given id.'});
  }
}));
```

## DELTE (Delete)
```js
app.delete('/subscriptions/:id', asyncHandler(async (req, res) => {
  const id = req.params.id;
  const sub = await Subs.findByIdAndDelete(id);//찾고 지우는 것까지 해준다.(편함)
  if(sub){
    res.sendStatus(204);//삭제 상태 코드는 204.
  } else{
    res.send({message : 'Cannot find given id.'});
  }
}));
```