모든 커밋에서 특정 파일 추적 취소

🧹 1. Git이 추적 중인 파일을 추적에서 제거하고 싶을 때

```bash
git rm --cached <파일명>
```

- `--cached` 옵션은 **파일은 로컬에 남기고**, Git의 추적만 제거해요.
- 이후 커밋하면 해당 파일은 Git에서 제거됩니다.

📁 2. 폴더 전체를 추적에서 제거하고 싶을 때

```bash
git rm --cached -r <폴더명>
```

🛑 3. 앞으로 특정 파일이나 폴더를 Git이 추적하지 않도록 설정하고 싶을 때

`.gitignore` 파일에 추가하세요:

# .gitignore 예시 config.json logs/ *.log

이미 추적 중인 파일은 `.gitignore`에 추가해도 자동으로 추적이 제거되진 않아요.  
그래서 `.gitignore` + `git rm --cached`를 함께 써야 완벽하게 제거돼요.

🔄 4. 제거 후 커밋하기

```bash
git commit -m "Remove tracked file from Git"
```

혹시 Git 추적을 지우고 싶은 이유가 `.gitignore` 설정 때문인지, 아니면 실수로 커밋한 민감한 파일 때문인지 알려주시면 더 정확하게 도와드릴 수 있어요! 😄
