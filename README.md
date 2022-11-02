# xv6_fork-exec
xv6 운영체제 forknexec 시스템 콜 구현

타깃 프로세스를 실행하는 시스템 콜 (Createprocess())

# 개발환경
Vmware/ubuntu-18.04.6-desktop-amd64

# 수정 및 작성한 소스코드
### forknexec.c (신규)
- forknexec 래퍼 함수가 정의된 파일

### Makefile
- 30line: forknexec 오브젝트 파일 추가
- 185line: forknexec 테스트용 유저 어플리케이션 추가
- 257line: forknexec 테스트용 유저 어플리케이션 추가

### defs.h
- 190line: forknexec 함수 선언

### syscall.h
- 23line: 시스템 콜 넘버 정의

### syscall.c
- 106line: forknexec 래퍼 함수 외부 참조 선언
- 130line: 시스템 콜 넘버를 래퍼함수의 주소와 연결

### user.h
- 26line: 유저 프로그램에 시스템 콜 함수가 보이게 등록

### usys.S
- 32line: 매크로 등록

### forknexec_text.c (신규)
- forknexec 테스트용 유저 어플리케이션 코드

### proc.c
- 537line~: forknexec 함수 정의

# 발생한 문제 및 수정 방법
### 문제
- fork 호출후 자식 프로세스 컨텍스트에 접근 불가능
- 자식프로세스 실행 컨텍스트가 유저
어플리케이션으로 바로 넘어감
- trap 은 상태를 저장하지 않기 때문에 syscall 내부에서
syscall 을 호출할 경우 첫 번째 syscall 의 실행 컨텍스트를
저장하지 않기 때문에 발생하는 문제로 보임
### 해결
- 2 번의 trap 을 거치지 않고 forknexec 에서 프로세스
생성 후, 자식에서 자식의 pid 를 리턴, 부모에서 exec 를
실행하는 형식으로 구현