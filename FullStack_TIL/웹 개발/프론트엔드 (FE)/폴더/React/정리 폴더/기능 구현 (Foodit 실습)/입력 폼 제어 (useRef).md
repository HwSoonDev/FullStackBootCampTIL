```JSX
import { useRef } from 'react';

function FileInput({ name, value, onChange }) {
  const inputRef = useRef();
  
  const handleChange = (e) => {
    const nextValue = e.target.files[0];
    onChange(name, nextValue);
  };

  const handleClearClick = () => {
    const inputNode = inputRef.current;
    if(!inputNode) return;
    
    inputNode.value='';
    onChange(name, null);
  };
    
  return (
    <div>
      <input type="file" onChange={handleChange} ref={inputRef}/>
      {value && <button type="button" onClick={handleClearClick}>
        X
      </button>}
    </div>
  );
}
```

useRef를 이용해서 입력 폼 노드에 접근한 모습이다.

1) input의 value(파일)가 채워지면, onChange에서 value를 상태 저장해서 버튼을 활성화 해주고,
2) 버튼을 눌러 취소하면 노드.value에 접근해 값을 지워준다. 

**입력 폼은 제어 컴포넌트로 만들 수 없기 때문**에 레퍼런스 참조로 노드 value를 제어 해주는 듯 하다.

