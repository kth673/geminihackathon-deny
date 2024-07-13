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

## 코드 작성 및 배포

### 1. Firebase Cloud Function 코드

<details>
  <summary><string>### `index.html`</string></summary>



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Image Analysis</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            line-height: 1.6;
            color: #333;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f4f4f4;
        }
        h1 {
            text-align: center;
            color: #2c3e50;
        }
        .upload-container {
            background-color: #fff;
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        #dragDropArea {
            border: 2px dashed #3498db;
            border-radius: 8px;
            padding: 40px;
            text-align: center;
            cursor: pointer;
            transition: background-color 0.3s;
            margin-bottom: 20px;
        }
        #dragDropArea.dragover {
            background-color: #ecf0f1;
        }
        #imagePreview {
            max-width: 100%;
            max-height: 300px;
            margin: 20px auto;
            display: none;
            border-radius: 4px;
        }
        #imageInput {
            display: none;
        }
        .btn {
            display: inline-block;
            padding: 10px 20px;
            background-color: #3498db;
            color: white;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s;
            border: none;
            font-size: 16px;
        }
        .btn:hover {
            background-color: #2980b9;
        }
        #analyzeButton {
            display: block;
            width: 100%;
            background-color: #2ecc71;
        }
        #analyzeButton:hover {
            background-color: #27ae60;
        }
        #uploadNewButton {
            display: none;
            margin: 10px auto;
        }
        #loading {
            text-align: center;
            display: none;
            color: #3498db;
            font-weight: bold;
        }
        #result {
            background-color: #fff;
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            margin-top: 20px;
            display: none;
        }
    </style>
</head>
<body>
    <h1>Image Handsomeness Analyzer</h1>
    <div class="upload-container">
        <div id="dragDropArea">
            <p>Drag and drop an image here, or click to select a file</p>
            <label for="imageInput" class="btn">Choose an Image</label>
        </div>
        <input type="file" id="imageInput" accept="image/*" required>
        <img id="imagePreview" alt="Image Preview" src="">
        <button id="analyzeButton" class="btn" type="button">Analyze Image</button>
        <button id="uploadNewButton" class="btn" type="button">Reset</button>
    </div>
    <div id="loading">Analyzing...</div>
    <div id="result"></div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.3/firebase-app.js";
        import { getAnalytics } from "https://www.gstatic.com/firebasejs/10.12.3/firebase-analytics.js";
        import { initializeAppCheck, ReCaptchaV3Provider, getToken } from "https://www.gstatic.com/firebasejs/10.12.3/firebase-app-check.js";

        const firebaseConfig = {
            apiKey: "YOUR_FIREBASE_API_KEY",
            authDomain: "YOUR_FIREBASE_AUTH_DOMAIN",
            projectId: "YOUR_FIREBASE_PROJECT_ID",
            storageBucket: "YOUR_FIREBASE_STORAGE_BUCKET",
            messagingSenderId: "YOUR_FIREBASE_MESSAGING_SENDER_ID",
            appId: "YOUR_FIREBASE_APP_ID",
            measurementId: "YOUR_FIREBASE_MEASUREMENT_ID"
        };

        const app = initializeApp(firebaseConfig);
        const analytics = getAnalytics(app);

        const appCheck = initializeAppCheck(app, {
          provider: new ReCaptchaV3Provider('YOUR_RECAPTCHA_SITE_KEY'),
          isTokenAutoRefreshEnabled: true
        });

        const imageInput = document.getElementById('imageInput');
        const imagePreview = document.getElementById('imagePreview');
        const analyzeButton = document.getElementById('analyzeButton');
        const uploadNewButton = document.getElementById('uploadNewButton');
        const resultDiv = document.getElementById('result');
        const loadingDiv = document.getElementById('loading');
        const dragDropArea = document.getElementById('dragDropArea');

        function handleFiles(files) {
            if (files.length > 0) {
                const file = files[0];
                if (file.type.startsWith('image/')) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        imagePreview.src = e.target.result;
                        imagePreview.style.display = 'block';
                        dragDropArea.style.display = 'none';
                        uploadNewButton.style.display = 'block';
                    }
                    reader.readAsDataURL(file);
                    imageInput.files = files;
                } else {
                    alert('Please select an image file.');
                }
            }
        }

        imageInput.addEventListener('change', function(event) {
            handleFiles(event.target.files);
        });

        dragDropArea.addEventListener('dragover', function(e) {
            e.preventDefault();
            e.stopPropagation();
            this.classList.add('dragover');
        });

        dragDropArea.addEventListener('dragleave', function(e) {
            e.preventDefault();
            e.stopPropagation();
            this.classList.remove('dragover');
        });

        dragDropArea.addEventListener('drop', function(e) {
            e.preventDefault();
            e.stopPropagation();
            this.classList.remove('dragover');
            handleFiles(e.dataTransfer.files);
        });

        dragDropArea.addEventListener('click', function() {
            imageInput.click();
        });

        uploadNewButton.addEventListener('click', function() {
            imagePreview.style.display = 'none';
            dragDropArea.style.display = 'block';
            uploadNewButton.style.display = 'none';
            imageInput.value = '';
            resultDiv.style.display = 'none';
        });

        analyzeButton.addEventListener('click', async function(event) {
            event.preventDefault();
            event.stopPropagation();

            if (!imageInput.files || imageInput.files.length === 0) {
                alert('Please select an image file.');
                return;
            }

            const formData = new FormData();
            formData.append('image', imageInput.files[0]);

            resultDiv.style.display = 'none';
            loadingDiv.style.display = 'block';

            try {
                const appCheckToken = await getToken(appCheck, false);        
                const response = await fetch('https://analyzeimage-jvta7gywha-uc.a.run.app/analyzeImage', {
                    method: 'POST',
                    body: formData,
                    headers: {
                        'X-Firebase-AppCheck': appCheckToken.token
                    },
                    mode: 'cors'
                });

                if (!response.ok) {
                    throw new Error('Network response was not ok: ' + response.statusText);
                }

                const data = await response.json();

                console.log("RECEIVED", data);

                resultDiv.innerHTML = `
                    <h3>Analysis Result:</h3>
                    <p><strong>Score:</strong> ${data.score}</p>
                    <p><strong>Reason:</strong> ${data.reason}</p>
                `;
            } catch (error) {
                console.error('Error:', error);
                resultDiv.innerHTML = 'An error occurred while analyzing the image: ' + error.message;
            } finally {
                loadingDiv.style.display = 'none';
                resultDiv.style.display = 'block';
            }

            return false;
        });

        document.addEventListener

('submit', function(event) {
            event.preventDefault();
            return false;
        });

        window.onbeforeunload = function() {
            return "Are you sure you want to leave this page?";
        };
    </script>
</body>
</html>
```
</details)
## 3. Firebase로 배포하기

### 프론트엔드 배포하기
- [Firebase Hosting Quickstart](https://firebase.google.com/docs/hosting/quickstart?_gl=1*10e86in*_up*MQ)

### 백엔드 배포하기
- [Firebase Cloud Functions 배포 가이드](https://firebase.google.com/docs/functions/get-started)




