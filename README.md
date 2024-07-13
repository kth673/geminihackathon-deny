# Gemini API & Firebase를 이용한 외모 점수 웹페이지 만들기


### Google IDX
- https://idx.google.com/

### Gemini
- Create API Key: http://goo.gle/aistudio-korea-YT
- API 가이드: https://ai.google.dev/gemini-api/docs/get-started/tutorial?lang=node&hl=ko

### Firebase
1. [구글 클라우드 가입](https://cloud.google.com/?hl=ko)
2. [구글 애널리틱스 가입 (선택)](https://analytics.google.com/analytics/web/?pli=1#/)
3. [Firebase 가입 및 프로젝트 생성](https://firebase.google.com/)(Functions 사용을 위해 Blaze 요금제로 변경)
   - [사용량](https://firebase.google.com/pricing?hl=ko) 초과시 요금 부과 되니 주의하여 사용
   - <details>
        <summary>구글 클라우드 결제설정(Firebase선택)</summary>
        <img src="https://github.com/user-attachments/assets/2e556a93-1ab4-48b9-8b4d-e61a7b7004c9">
    </details>   
4. [Firebase 설정(2~3까지 진행)](https://firebase.google.com/docs/functions/get-started?hl=ko&_gl=1*pqsuxx*_up*MQ&gen=2nd#set-up-your-environment-and-the-firebase-cli)
    - Installtion Package Add
      ```sh
      npm install busboy cors sharp --save
      ```
5. Firebase Cloud Functions 백엔드 응답확인([link](https://firebase.google.com/docs/functions/http-events?hl=ko&gen=2nd#node.js))
      ```
      const { onRequest } = require("firebase-functions/v2/https");
      
      exports.sayHello = onRequest(
        { cors: [/firebase\.com$/, "flutter.com"] },
        (req, res) => {
          res.status(200).send("Hello world!");
        }
      );
      ```
6. 로컬 테스트:
    ```sh
    firebase init emulators
    firebase emulators:start
    ```
7. 코드 작성
    - 프론트엔드(public/index.html)
    - 백엔드(/functions/index.js)
      
8. Firebase 배포
   - 프론트엔드([Firebase Hosting Quickstart](https://firebase.google.com/docs/hosting/quickstart?_gl=1*10e86in*_up*MQ))
   - 백엔드([Firebase Cloud Functions Deploy](https://firebase.google.com/docs/functions/get-started?hl=ko&gen=2nd#deploy-functions-to-a-production-environment))

9. Web 결과 확인

[출처] : 조코딩([YouTube](https://www.youtube.com/live/ltm6r3dZ4Ag?si=fobWLaFzT5TBY6s5))
