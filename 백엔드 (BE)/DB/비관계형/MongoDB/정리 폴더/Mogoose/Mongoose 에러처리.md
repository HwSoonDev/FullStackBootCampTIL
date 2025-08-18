```js
function asyncHandler(handler) {
  // 여기에 코드를 작성하세요.
  return async function (req, res){
    try{
      await handler(req, res);
    }catch (e){
      //console.log(e.name);
      //console.log(e.message);
      if(e.name == "ValidationError"){
        return res.status(400).send({message: e.message});
      }
      if(e.name == "CastError"){
        return res.status(404).send({message: 'Cannot find given id.'});
      }
      return res.status(500).send({message: e.message});
    }
  }
  //return asyncReqHandler;
}
```