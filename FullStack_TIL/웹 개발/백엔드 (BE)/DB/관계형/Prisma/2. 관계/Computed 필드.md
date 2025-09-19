
api 핸들러에서,
필드의 값들을 계산해서 새로운 값을 만들고
새로운 필드로 추가해줄 수 있다.

```js
//...
let total = 0;
order.ordrItmes.forEach((orderItem) => {
	total += orderItem.unitPrice * orderItem.quantity;
});
order.total = total;
//...
```