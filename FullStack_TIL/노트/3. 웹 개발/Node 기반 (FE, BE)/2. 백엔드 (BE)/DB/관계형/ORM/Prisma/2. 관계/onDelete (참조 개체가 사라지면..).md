참조하는 (관계를 가지는) 개체가 사라지면 어떻게 처리할 지를 정의하는 옵션.

## 1) Restrict
특정 데이터를 참조하는 데이터가 있으면 삭제할 수 없다.
![[Pasted image 20250918185126.png]]
### 2) SetNull
Foriegn Key(외래키)가 가리키는 데이터가 삭제되면 Foreign Key를 Null로 설정.
![[Pasted image 20250918185318.png]]
### 3) SetDefault
외래키가 삭제되면 디폴트 값으로 설정.
![[Pasted image 20250918185409.png]]