**커밋 형태로 되돌리기**

| git reset [옵션] c1                        | working deirectory | staging area | repository                                    |
| ---------------------------------------- | ------------------ | ------------ | --------------------------------------------- |
| <span style='color: blue'>--soft</span>  | X                  | X            | <span style='color: red'>HEAD</span>가 커밋을 가리킴 |
| <span style='color: blue'>--mixed</span> | X                  | 커밋 내용물로 바뀜   | <span style='color: red'>HEAD</span>가 커밋을 가리킴 |
| <span style='color: blue'>--hard</span>  | 커밋 내용물로 바뀜         | 커밋 내용물로 바뀜   | <span style='color: red'>HEAD</span>가 커밋을 가리킴 |
보통 작업물까지 다시 작업하기 위해 --hard 옵션을 일반적으로 사용한다. 

git reset --hard 7f3d                         커밋id 위치로 리셋

git reset --hard HEAD^                    헤드 바로 전 커밋 위치로 리셋

git reset --hard HEAD~2                  헤드 2단제 전 커밋 위치로 리셋


![[git reset 옵션.png]]