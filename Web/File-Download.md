# File Download 

## 문제 설명

File Download 취약점이 존재하는 웹 서비스에서 flag.py를 다운로드하는 문제

---

## 관련 개념

### File Download 취약점

사용자가 입력한 파일명을 검증하지 않고 다운로드할 경우,
의도하지 않은 파일을 읽을 수 있는 취약점이다

### Path Traversal

../ 를 이용하여 상위 디렉터리로 이동하는 공격 기법이다

---

## 코드 분석

### 사용자 입력

filename = request.args.get("name")

사용자가 URL에서 전달한 파일명을 가져온다


### 취약한 코드

with open(f'{UPLOAD_DIR}/{filename}', 'rb') as f:

업로드와 다르게 사용자가 입력한 filename을 검증하지 않고 그대로 사용한다

---

## 취약점

/read?name=../flag.py

↓

uploads/../flag.py

↓

flag.py

를 읽게 된다

---

## 문제 풀이

1. 메모 업로드
2. Home에서 Read 확인
3. URL 확인
4. ../flag.py 입력
5. Flag 획득

---


## 배운 점

- request.args.get()
- ../ 의 의미
- Path Traversal
