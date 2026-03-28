### DOM 노드 참조

```JSX
import { useRef } from 'react'; 

function Image({ src }) { 

	const imgRef = useRef();
	
	const handleSizeClick = () => { 
		const imgNode = imgRef.current; 
		if (!imgNode) return; 
		const { width, height } = imgNode; console.log(`${width} x ${height}`); 
	}; 
	return ( 
		<div> 
			<img src={src} ref={imgRef} alt="크기를 구할 이미지" /> 
			<button onClick={handleSizeClick}>크기 구하기</button> 
		</div> 
	); 
}
```

DOM 노드 참조를 저장하고 접근할 수 있다. 
레퍼런스로 만든 객체의 .current로 노드에 접근할 수 있다.