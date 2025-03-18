## 쿠키 
- 웹사이트가 사용자의 브라우저에 저장하는 작은 데이터 조각
- 주로 사용자 상태를 유지하거나, 로그인 정보 및 사용자 설정을 저장

### 쿠키의 주요 특징 
- 클라이언트(브라우저)에 저장됨
- 세션 유지에 사용됨 (로그인 상태, 장바구니 정보 유지)
- 작은 크기 (최대 4KB)
- 만료 기한 설정 가능
- 보안 옵션 존재(HttpOnly, Secure, Sametime) 

### 쿠키의 종류 
#### 1. 세션 쿠키
 - 브라우저를 닫으면 자동 삭제됨
 - Expires 또는 Max-Age 를 설정하지 않으면 자동으로 세션 쿠키가 됨
 - 로르인 세션 등에 사용됨
#### 2. 지속 쿠키
 - 지정된 만료 날짜까지 유지됨
 - Expires 또는 Max-Age를 설정하여 저장 가능
 - 로그인 유지, 사용자 설정 저장 등에 사용됨
#### 3. 보안 쿠키
 - HTTPS에서만 전송됨(Secure 속성 필요)
 - 네트워크 공격 방지
#### 4. HttpOnly 쿠키
 - JavaScript에서 접근 불가능 (HttpOnly 속성 필요)
 - XSS 공격 방지
#### 5. SameSite 쿠키
 - 크로스 사이트 요청 제한 (SameSite 속성 필요)
 - SameSite=Strict → 다른 사이트에서는 절대 전송 안 함
 - SameSite=Lax → 안전한 요청(GET)에서는 전송
 - SameSite=None; Secure → 크로스 사이트에서도 허용 (단, HTTPS 필요)

### 쿠키 구조 
Set-Cookie: refreshToken=abc123; HttpOnly; Path=/; Max-Age=604800; Secure; SameSite=None;

- HTTPS에서만 전송 (Secure)
- JavaScript에서 접근 불가 (HttpOnly)
- 모든 경로에서 사용가능 Path=/
- 7일 동안 유지 (Max-Age=604800)
- 크로스 사이트 요청에서도 전송 가능(SameSite=None)
- 


