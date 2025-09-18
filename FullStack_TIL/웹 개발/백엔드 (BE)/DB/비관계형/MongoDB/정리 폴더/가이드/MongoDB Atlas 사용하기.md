myDB_1MongoDB Atlas 사용법

MongoDB Atlas에 가입하고 데이터베이스 주소를 사용하는 방법에 대해 알아봅시다.

MongoDB Atlas를 사용하려면 가장 먼저 회원 가입을 해야 합니다. [Atlas 회원 가입 페이지](https://www.mongodb.com/cloud/atlas/register)로 가서 회원 가입 폼을 작성하거나 구글 계정으로 로그인을 해주세요 (회원 가입 시 이메일 인증이 필요할 수도 있습니다).

![[Pasted image 20250816164318.png]]

회원가입을 완료하면 온보딩 화면이 뜹니다. 사용 용도에 따라 폼을 작성하고 'Finish 버튼을 클릭해 주세요.

![[Pasted image 20250816164329.png]]

그다음에는 클러스터, 즉 클라우드 서비스에 대한 세팅을 해야 합니다. 학습 용도로 가볍게 사용할 거라면 무료 버전을 선택하시고 'Quick Setup' 부분을 사진처럼 설정해 주세요. Name, Provider, Region은 자유롭게 설정하시면 됩니다. 이후에 'Create Deployment' 버튼을 클릭해서 클러스터를 생성해 주세요.

![[Pasted image 20250816164339.png]]

다음으로 보안 관련 설정을 해야 하는데요. 클러스터 연결을 허용할 IP가 현재 IP Address로 자동 등록됩니다. 사용할 유저명과 비밀번호도 자동 생성되는데 변경이 필요하면 변경을 해주세요. 데이터베이스에 접속할 때 유저네임과 비밀번호가 필요하기 때문에 **둘 다 안전한 곳에 기록해 두세요**. 모두 입력했으면 'Create Database User' 버튼을 눌러주세요.

![[Pasted image 20250816164405.png]]

다음과 같이 완료되었다면 'Close' 버튼을 클릭합니다.

![[Pasted image 20250816164352.png]]

이제 클러스터에 데이터베이스와 컬렉션을 실제로 생성할 겁니다. 좌측 메뉴의 'Clusters'로 이동한 다음 'Browse Collections'를 클릭하세요.

![qvilye0gy-image.png](https://codeit-assets.s3.ap-northeast-2.amazonaws.com/tutorials/70/qvilye0gy-image.png)

'Add My Own Data' 버튼을 클릭하세요.

![[Pasted image 20250816164412.png]]

데이터베이스 이름과 컬렉션 이름을 입력하고 'Create' 버튼을 클릭하세요.

![[Pasted image 20250816164421.png]]![[Pasted image 20250816164429.png]]

데이터베이스가 잘 생성됐으면 좌측 메뉴의 'Clusters'로 이동한 다음 'Connect'를 클릭하세요.

![[Pasted image 20250816164435.png]]

Drivers 옵션을 클릭하세요.

![[Pasted image 20250816164441.png]]

3번 아래 있는 URL을 복사해 주세요.

![[Pasted image 20250816164456.png]]

이제 Express나 Next.js 같은 프로그램에서 이 URL을 통해 MongoDB 데이터베이스에 접속할 수 있는 겁니다. 잘 복사해 뒀다가 필요한 부분에 붙여 넣어 주세요.

붙여 넣을 때 `codeit`, `<db_password>` 부분을 본인의 기록해 둔 유저명과 비밀번호로 대체하고 `mongodb.net/` 뒤에 데이터베이스 이름(예: `todo-api`)을 써줘야 합니다. 예를 들어 아이디가 `codeit`, 비밀번호가 `test123`이고 데이터베이스 이름이 `todo-api`라면 아래와 같은 형태가 되는 거예요.