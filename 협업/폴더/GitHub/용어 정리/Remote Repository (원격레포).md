**Remote Repository (=GitHub)**

깃을 깃허브에 올린다고 할 때 우리는 로컬에 있는 레포지토리를 리모트 레포지토리에 올리는 것이다.

이미 만든 로컬 레포지토리를 깃허브에 올리기 위해서는 위 사진의 초록색 박스 부분 내용을 입력하면 된다. (깃허브 계정과 올릴 원격 레포지토리 생성이 먼저 필요하다.)

##### git remote add origin [https://github.com/계정이름/원격레포지토리이름.git](https://github.com/계정이름/원격레포지토리이름.git)

origin을 원격 레포의 이름으로 설정

##### git push -u origin master

-u :  원격 레포 origin의 브랜치 master를 로컬의 브랜치 master 로 추적(trcking)하겠다는 옵션 (-u == --set-uptream)

만약 -u 옵션을 설정하지 않으면 앞으로 push, pull 할 때 계속

git push origin master:mater 처럼 써서 로컬 브랜치와 원격 브랜치를 계속 이어줘야한다.