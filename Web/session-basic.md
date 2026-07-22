# session-basic

## 문제 설명

- 쿠키(Cookie)와 세션(Session)을 이용한 로그인 인증 방식을 이해하는 문제
- 서비스는 guest, user, admin 계정을 가지고 있으며, 로그인에 성공하면 서버에서 세션을 생성하고
브라우저에는 세션 ID를 쿠키로 저장
- 최종 목표는 admin 권한으로 인증되어 플래그를 획득하는 것
  

---

## 관련 개념

### Cookie

- 브라우저가 저장하는 데이터
- 이 문제에서는 session id만 저장

### Session

- 사용자의 로그인 정보를 서버에서 관리하는 방식
- 이 문제에서는 실제 username은 서버의 session_storage에서 관리

### Session Hijacking

- 다른 사용자의 Session ID를 획득하여 자신의 쿠키에 넣음으로써 해당 사용자인 것처럼 인증받는 공격

### Information Disclosure

- 민감한 정보를 사용자에게 그대로 노출하는 취약점
- 이번 문제에는 admin의 Session ID가 그대로 노출
  

---

## 코드 분석

### 사용자 정보

`user = { "guest": "guest", "user": "user1234","admin": FLAG }`

admin의 비밀번호는 FLAG로 설정되어있기 때문에 정상적인 로그인은 사실상 불가

### 로그인 성공 시
```python
session_id = os.urandom(32).hex()
session_storage[session_id] = username
resp.set_cookie("sessionid", session_id)
```

로그인 성공 시  
- 랜덤한 Session ID 생성
- Session Storage에 저장
- 브라우저 Cookie에 Session ID 저장

### 메인 페이지
```python
session_id = request.cookies.get("sessionid")
username = session_storage[session_id]
```

브라우저에서 sessionid를 가져온 후 
조회하여 username 얻음

### 서버 실행 시

`session_storage[os.urandom(32).hex()] = 'admin'`

프로그램이 실행될 때 admin의 Session ID도 랜덤하게 생성

---

## 취약점 분석

취약점은 Session ID 생성 방식이 아닌,
/admin 페이지에 있음 

관리자 인증을 수행하는 코드가 모두 주석 처리되어 있었고, 대신
return session_storage
를 통해 Session Storage 전체를 반환

즉 누구나 /admin에 접근하여 확인 가능 

결국 Information Disclosure로 인해 admin의 Session ID가 유출되고, 이를 이용한 Session Hijacking이 가능

---

## 문제 풀이

1. guest 게정 로그인
2. 소스코드 분석
3. /admin 엔드포인트 확인
4. Session Hijacking
5. 플래그 획득
   
---

## 배운 점

- Session 인증구조와 Session Hijacking 원리
- 엔드포인트 확인
