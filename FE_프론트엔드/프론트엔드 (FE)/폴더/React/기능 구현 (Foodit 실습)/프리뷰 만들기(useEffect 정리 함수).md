
```JSX
 useEffect(() => {
	 if(!value) return;
	 const objectURL = URL.createObjectURL(value);
	 setPreview(objectURL);
	 
	 return () => {
		setPreview();
		URL.revokeObjectURL(objectURL);
	 };
}, [value]);
```

URL.createObjectURL(value)을  사용하면, 이미지 파일을 url 객체로 만들어준다.
이 url을 이미지 src로 넣어주면 사이트에 올린 파일의 미리보기를 만들 수 있다.

하지만, createObjectURL()은 메모리를 계속 할당하고 있기 때문에 
수동으로 해제 해주지 않으면 가비지 메모리가 된다. (=> **사이드 이펙트**로 이어짐)

useEffect()는 먼저 setPreview()의 무한 실행을 막는 역할과 함께,
리턴에서 URL의 메모리를 해제하여 위에서 말한 사이드 이펙트를 해결하는 역할도 한다.

1) State 무한 실행
2) 사이드 이펙트 (가비지 메모리)

의존성 배열 = Dependency Array