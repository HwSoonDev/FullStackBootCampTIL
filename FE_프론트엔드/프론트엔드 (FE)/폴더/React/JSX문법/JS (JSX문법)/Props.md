JSX에서 만든 컴포넌트(함수)에 인자를 전달할 때, 태그의 속성처럼 작성하는데,
이것을 Props라고 한다. (Properties)
보통 props라고 파라미터 이름을 짓고, props.000으로 접근한다.

```
JSX 컴포넌트 사용법 = HTML 태그 프로퍼티  -> JS 함수 인자 전달 
```


**컴포넌트 스크립트 (HandIcon.js)**
```JSX
import rockImg from './assets/rock.svg';
import scissorImg from './assets/scissor.svg';
import paperImg from './assets/paper.svg';

function HandIcon(props) {
  let s = rockImg;
  switch(props.value){
    case 'rock':
      s = rockImg;
      break;
    case 'scissor':
      s = scissorImg;
      break;
    case 'paper':
      s = paperImg;
      break;
    default:
      break;
  }
  return <img src={s} alt={props.value} />;
}

export default HandIcon;
```

**컴포넌트에 인자를 전달하는 모습 (App.js)**
```JSX
import HandIcon from './HandIcon';

function App() {
  return (
    <div>
      <HandIcon value="rock" />
      <HandIcon value="scissor" />
      <HandIcon value="paper" />
    </div>
  );
}

export default App;
```

**main.js (index.js)**
```JSX
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

이제 컴포넌트를 배웠으니 파일을 분리해서 만들 것이다.
1) index.js(main.js)
2) App.js
3) 컴포넌트.js 

```JSX
function HandIcon({value, color}) {
	switch(value){
	}
}
```
props 대신에 **구조분해(destructuring)** 해서 바로 쓸 수도 있다.
