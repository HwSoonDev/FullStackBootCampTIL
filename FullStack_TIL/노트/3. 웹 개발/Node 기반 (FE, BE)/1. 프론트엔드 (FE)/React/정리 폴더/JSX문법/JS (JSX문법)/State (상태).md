리액트는 상태로 조작한다.
useState 함수를 통해 '상태'를 저장하고 변수 대신에 사용한다.
useState함수는 배열을 반환하는 데
1) 첫 번째 - 값이 담긴 변수
2) 두 번째 - Setter함수
를 반환한다.
상태를 변경할 때는, Setter 함수 만을 사용해야 한다.

(ueeState 함수는 react 패키지에서 불러온다.)

```JSX
import Button from './Button';
import HandButton from './HandButton';
import HandIcon from './HandIcon';
import { compareHand, generateRandomHand } from './utils';
import { useState } from 'react';

function getResult(me, other) {
  const comparison = compareHand(me, other);
  if (comparison > 0) return '승리';
  if (comparison < 0) return '패배';
  return '무승부';
}

function App() {
  const [hand, setHand] = useState('rock');
  const [otherHand, setOtherHand] = useState('rock');

  const handleButtonClick = (nextHand) => {
    setHand(nextHand);
    setOtherHand(generateRandomHand());
  };

  const handleClearClick = () => {
    setHand('rock');
    setOtherHand('rock');
  };

  return (
    <div>
      <Button onClick={handleClearClick}>처음부터</Button>
      <p>{getResult(hand, otherHand)}</p>
      <div>
        <HandIcon value={hand} />
        VS
        <HandIcon value={otherHand} />
      </div>
      <div>
        <HandButton value="rock" onClick={handleButtonClick} />
        <HandButton value="scissor" onClick={handleButtonClick} />
        <HandButton value="paper" onClick={handleButtonClick} />
      </div>
    </div>
  );
}

export default App;
```

##### 참조형 State - 배열을 State에 저장하면...

배열을  State에 저장하고 요소를 추가하면,
참조 값인 배열은 주소가 바뀌는 것은 아니기 때문에
 State가 변경된 것으로 처리 되지 않는다. (페이지 업데이트가 안된다.)

이 때는 새 배열(주소가 다른)을 넣는 등의 방법이 있다.

1) arr.push(newElm); setArr(arr); -> X(메모리 주소가 같아서 업데이트x)
2) setArr([...arr, newElm]); -> O(메모리 주소가 달라서 업데이트o)