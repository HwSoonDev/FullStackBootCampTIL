
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

className 프로파티는 한 개 클래스
classNames 프로퍼티는 여러 클래스 지정이 가능하다. (기존 html과 동일)

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