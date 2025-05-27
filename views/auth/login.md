### 인증

```javascript
// firebase/auth -> node_modules > firebase > auth
// 웹에서 참조하는 파일은 firebase-auth.js
// XXX.js안에는 변수, 클래스, 함수 포함
import { getAuth, signInWithEmailAndPassword } from "firebase/auth";
// 함수 호출로 객체를 주입받을 수 있다
const auth = getAuth();
// 비밀번호로 로그인을 처리하는 함수이다 - firebase auth가 제공하는 함수이다
// 그래서 import 해야한다 -module 방식 <-> commonJS(require)
// 파라미터 - auth는 getAuth()통해서 주입받음
// login.ejs에서 입력받은 이메일과 비번을 각각 두번째와 세번째 파라미터로 넣어줌
// 이 함수 요청으로 firebase에서 처리(시간지연발생-비동기이다)를 한다 
// - 성공하면(then) 이후가 진행됨(자동으로 진행됨 - 콜백) -> 서버측에서 응답이 있다
// 응답 결과를 호출해준 함수의 콜백부분에 파라미터 자리인 userCredential을 담아줌
signInWithEmailAndPassword(auth, email, password)
  .then((userCredential) => {
    // Signed in 
    const user = userCredential.user;
    // ...
  })
  .catch((error) => {
    const errorCode = error.code;
    const errorMessage = error.message;
  });
  ```