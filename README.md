# Gemini API & Firebase를 이용한 외모 점수 웹페이지 만들기

## 개발 환경

### Google IDX
- https://idx.google.com/

### Gemini
- Create API Key: http://goo.gle/aistudio-korea-YT
- API 가이드: https://ai.google.dev/gemini-api/docs/get-started/tutorial?lang=node&hl=ko

### Firebase
1. [구글 클라우드 가입 & 결제 설정](https://cloud.google.com/?hl=ko)
2. [구글 애널리틱스 가입 (선택)](https://analytics.google.com/analytics/web/?pli=1#/)
3. [Firebase 가입 및 프로젝트 생성](https://firebase.google.com/)
4. Firebase 설치하기: [설치 가이드](https://firebase.google.com/docs/functions/get-started?hl=ko&_gl=1*pqsuxx*_up*MQ)
5. Firebase Cloud Functions 사용하기: [설명서](https://firebase.google.com/docs/functions/http-events?hl=ko&gen=2nd#node.js)
6. 테스트 하기:
    ```sh
    firebase init emulators
    firebase emulators:start
    ```
### Installed Package
functions@ /home/user/geminihackathon-deny/functions
├── @google/generative-ai@0.14.1
├── busboy@1.6.0
├── cors@2.8.5
├── firebase-admin@12.2.0
├── firebase-functions-test@3.3.0
├── firebase-functions@5.0.1
└── sharp@0.33.4

## 3. Firebase로 배포하기

### 프론트엔드 배포하기
- [Firebase Hosting Quickstart](https://firebase.google.com/docs/hosting/quickstart?_gl=1*10e86in*_up*MQ)

### 백엔드 배포하기
- [Firebase Cloud Functions 배포 가이드](https://firebase.google.com/docs/functions/get-started)

</details>


