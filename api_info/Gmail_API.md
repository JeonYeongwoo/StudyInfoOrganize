### Q: 해당 파일에서 사용 가능한 gmail-api 키 발급받는 방법 
```
@Configuration
public class GoogleOAuthConfig {
    private static final JsonFactory JSON_FACTORY = GsonFactory.getDefaultInstance();

    @Value("${google.client-id}")   //google cloud client ID
    private String clientId;

    @Value("${google.client-secret}") //google cloud client 비밀번호
    private String clientSecrets;

    @Value("${google.scopes}") //사용자에게 요청할 권한 범위(일단 readonly만 설정되어있음.)
    private String scope;
...
```

---------------------------------
### A: GPT Answer<br><br>
좋아요 👍 — 이제 Google OAuth 2.0 Client ID와 Client Secret을 직접 발급받는 방법을 단계별로 알려드릴게요.
이건 Google Cloud Console(https://console.cloud.google.com)에서
 진행해야 합니다.

✅ 1. Google Cloud Console 접속

👉 https://console.cloud.google.com

Google 계정으로 로그인합니다.

처음이라면 “프로젝트 선택” 창이 뜰 거예요.
기존 프로젝트를 써도 되고, “새 프로젝트 만들기”를 눌러 새로 만들어도 됩니다.

✅ 2. 프로젝트 만들기 (필요한 경우)

좌측 상단 “프로젝트 선택” → “새 프로젝트”

이름 아무거나 (예: MailTestServer)

만들기 클릭

✅ 3. API 및 서비스 활성화

좌측 메뉴에서 “API 및 서비스 → 라이브러리” 클릭

검색창에 “Gmail API” 또는 “Google OAuth” 입력

Gmail API 선택 후 “사용” 버튼 클릭
(MailTestServer가 Gmail 계정에 접근할 수 있도록 API 허용)

✅ 4. OAuth 동의 화면 구성

좌측 메뉴 → “API 및 서비스 → OAuth 동의 화면”

사용자 유형 선택:

개인용 테스트라면 외부(External) 선택

앱 이름, 사용자 이메일 주소 입력

“범위(Scope)” 단계에서
https://www.googleapis.com/auth/gmail.readonly 등 필요한 범위 추가

테스트 사용자에 본인 이메일 주소 추가

저장 및 계속 → 완료

✅ 5. OAuth 클라이언트 ID 발급

좌측 메뉴 → “API 및 서비스 → 사용자 인증 정보(Credentials)”

상단 “사용자 인증 정보 만들기” → “OAuth 클라이언트 ID”

애플리케이션 유형 선택:

웹 애플리케이션(Web application) 선택

이름 예: MailTestServer

승인된 리디렉션 URI 추가:

http://localhost:8080/login/oauth2/code/google


(Spring Boot 앱이 로컬에서 OAuth callback을 받는 주소입니다)

만들기(Create) 클릭

✅ 6. 발급된 Client ID / Secret 확인

생성 완료 후 팝업창에 이렇게 나옵니다:

클라이언트 ID:  1234567890-abcxyz.apps.googleusercontent.com
클라이언트 보안 비밀:  GOCSPX-abcdefg12345


이 값을 복사해서
application.properties 에 넣습니다:

google.client-id=1234567890-abcxyz.apps.googleusercontent.com
google.client-secret=GOCSPX-abcdefg12345
google.scopes=https://www.googleapis.com/auth/gmail.readonly

✅ 7. 테스트 실행

Spring Boot 프로젝트 실행
(MailTestServerApplication → Run)

브라우저에서 아래 주소로 이동:

http://localhost:8080


구글 로그인 → 권한 요청 → 승인하면
애플리케이션이 access token을 받아 Gmail API 호출 가능해집니다.

💡 추가 팁

테스트 단계에서는 “OAuth 동의 화면”에 테스트 사용자 이메일을 등록해야 로그인이 됩니다.

배포할 때는 Google에 “앱 검수” 요청을 해야 일반 사용자에게도 동작합니다.

원하신다면 🔐 지금 사용 중인 프로젝트(MailTestServer)에 맞게
“Google OAuth Client ID 발급 후 연결 테스트용 Controller” 예제도 만들어드릴까요?
(예: /google/login → 로그인 후 /google/callback 으로 토큰 받아오는 흐름)

<br>

application.properties 예시
---------------------------------
```
# ===============================
# Google OAuth Configuration
# ===============================

google.client-id=YOUR_GOOGLE_CLIENT_ID
google.client-secret=YOUR_GOOGLE_CLIENT_SECRET
google.scopes=https://www.googleapis.com/auth/gmail.readonly
google.redirect-uri=http://localhost:8080/login/oauth2/code/google
```
