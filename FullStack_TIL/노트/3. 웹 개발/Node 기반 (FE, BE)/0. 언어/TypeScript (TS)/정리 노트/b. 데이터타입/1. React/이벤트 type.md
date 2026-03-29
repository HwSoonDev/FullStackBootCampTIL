
```ts
import React from 'react';

// 1. MouseEvent - 마우스 관련 이벤트
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
  console.log('클릭됨:', e.button);
}

// 2. FormEvent - 폼 관련 이벤트
function handleSubmit(e: React.FormEvent<HTMLFormElement>) {
  e.preventDefault();
  console.log('폼 제출됨');
}

// 3. ChangeEvent - 입력값 변경 이벤트
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
  console.log('입력값:', e.target.value);
}

// 4. KeyboardEvent - 키보드 이벤트
function handleKeyDown(e: React.KeyboardEvent<HTMLInputElement>) {
  if (e.key === 'Enter') {
    console.log('엔터키 눌림');
  }
}
```