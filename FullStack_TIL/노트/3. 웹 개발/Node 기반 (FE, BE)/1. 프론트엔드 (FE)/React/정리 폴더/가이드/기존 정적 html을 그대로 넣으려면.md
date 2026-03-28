리액트 기반 사이트에 기존의 **HTML, CSS, JavaScript로 만든 정적 웹 페이지**를 **수정 없이 그대로 포함하거나 재사용**하려면 다음과 같은 방법들을 사용할 수 있습니다. 어떤 방식이 최적인지는 사이트 구조와 사용 목적에 따라 다릅니다.

---

## ✅ 1. **React App 내부에 정적 파일로 포함하기 (가장 간단함)**

### 📁 예: public 폴더에 기존 파일 넣기

React에서는 `public/` 폴더 안에 있는 파일은 **그대로 서빙**됩니다.  
즉, 기존의 HTML + CSS + JS 파일이 있다면 `public/legacy/` 폴더에 넣고 링크로 접근할 수 있습니다.

### 📦 폴더 구조 예시:

```pgsql
my-react-app/
├── public/
│   └── legacy/
│       ├── old.html
│       ├── styles.css
│       └── script.js
├── src/
│   └── App.jsx
```
### 🔗 사용 방법

- 브라우저에서 접근: `http://localhost:3000/legacy/old.html`
    
- React Router와도 별도로 동작함 (React 라우팅과 충돌 안 함)