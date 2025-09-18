classNames 라이브러리를 설치하면
여러 클래스를 편하게 넘길 수 있다.

	npm install classNames

```JSX
import classNames from 'classnames'; 

function Button({ isPending, color, size, invert, children }) { 
	return ( 
		<button className={
			classNames( 
				'Button', 
				isPending && 'pending', 
				color, size, 
				invert && 'invert', 
			)}>
			{ children } 
		</button > ); 
} 

export default Button;
```

(번외+)
&& 논리 연산자는 앞이 true일 경우 뒤를 그대로 반영한다는 특성이 있어서 활용한 듯 하다.
''는 false와 같아서 오류가 안 나는 듯하다...


안 쓴 예
```JSX
function Button({ isPending, color, size, invert, children }) { 
	const classNames = `Button ${isPending ? 'pending' : ''} ${color} ${size} ${invert ? 'invert' : ''}`; 
	return 
		<button className={classNames}>{children}</button>; 
} 

export default Button;
```