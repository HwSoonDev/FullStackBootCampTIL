
JSX 소스코드에서
import 'css파일 경로' 만 적어주면,
해당 소스코드 안 컴포넌트 HTML에 전부 적용된다.

```JSX
import HandIcon from './HandIcon';
import './HandButton.css';

// CSS 파일로 스타일을 적용해 주세요
function HandButton({ value, onClick }) {
	const handleClick = () => onClick(value);
	return (
		<button className='HandButton'  onClick={handleClick}>
		      <HandIcon className='HandButton-icon' value={value} />
				</button>
	);
}

export default HandButton;
```

className 프로파티는 여러 개 클래스도 된다.

## classNames

classNames 프로퍼티는 클래스 이름과, 가지고 있는 속성까지 태그 탐색에 쓸 수 있다.
라이브러리 임포트가 필요하다.

```JSX
import './Button.css';

function Button({children, onClick, color = 'blue', className = ''}){
	const classNames = `Button ${color} ${className}`; 
	//리액트 객체(html) 안에서는 백픽(`)이 안된다.
	return (
	    <button classNames={classNames} onClick={onClick}>
	         {children}
	    </button>
   );
}
```