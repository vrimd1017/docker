# docker
#### 개인적으로 docker를 공부하면서 정리한 글.

--- 

## 목록
- 1. 도커 다운 및 설치
- 2. 기초 명령어
- - 1) 이미지 조회, 삭제, 다운 
- - 2) 도커 컨테이너 조회, 생성, 삭제, 실행
- - 3) 도커 이미지 커밋
- 3. 도커 컨테이너간 통신
- 4. 도커의 LifeCycle

---

### 1. 도커 다운 및 설치

---

### 2. 기초 명령어 

#### 1) 이미지 조회, 삭제, 다운
#### 2) 도커 컨테이너 조회, 생성, 삭제, 실행
#### 3) 도커 이미지 커밋

---

### 3. 도커 컨테이너간 통신

---

### 4. 도커의 LifeCycle

---


docker images
현재 가진 도커이미지 리스트 출력

docker ps
:: 동작중인 컨테이너 확인

docker ps -a[all]
:: 정지된 컨테이너 확인. 삭제 이후 삭제되었는지 확인 // 모든 컨테이너 확인

docker rm [컨테이너 id[,컨테이너 id, 컨테이너 id]]
:: 컨테이너 삭제

docker rm `docker ps -a -q`
:: 컨테이너 모두 삭제

//

docker images
:: 현재 이미지 확인

docker rmi [이미지 id]
:: 이미지 삭제

docker rmi -f [이미지 id]
:: 컨테이너가 삭제되지 않은상태에서 이미지를 삭제. 이때 -f 옵션이 있다면 컨테이너도 강제삭제


////

컨테이너 생성시 --name옵션으로 이름을 설정. 아닐경우 도커엔진이 임의로 형용사/명사조합.
이름은 id와 마찬가지로 중복이 안됨. 
docekr rename명령어로 이름 변경이 가능.


////
LifeCycle
생성(create) >> 시작(start) >> 실행(running) >> 중지(stopped) >> 삭제(deleted)
create >> docker create <이미지이름>
start >> docker start <시작할 컨테이너 아이디/이름>
running >> docker run <이미지 이름>
stop >> docker stop <중지할 컨테이너 아이디/이름>
deleted >> docker rm <삭제할 컨테이너 아이디/이름>

:: docker run == docker create + docker start

///
docker start -a <컨테이너아이디>
--attatch :: -a옵션이 없을경우 start가 되지 않음.
-a옵션의 역할은 실행이 될 때, 컨테이너에 붙어있는 다음 출력으로 나오는 아웃풋들을 화면에 표출.

///
stop/kill
stop:: gracefully 하게중지. 하던작업들을 완료하고 컨테이너 중지.
kill:: 바로 컨테이너 중지.

docker stop >> SIGTERM -- SIGKILL 
docker kill >> SIGKILL

/////////

실행중인 컨테이너에 명령 전달
docker exec <컨테이너 아이디>
run :: 새로 컨테이너를 만들어서 실행
exec :: 이미 실행중인 컨테이너에 명령 전달.

docker exec -it <컨테이너아이디> <전달할 명령어> sh
-it :: 명령어를 수행한 후 bash쉘에서 나와버림. -it옵션을 통해 계속 명령어를 적을 수 잇음.
-sh
명령어 하나를 입력해야 할 때마다 모든 것을 계속 입력해줘야함.
컨테이너 밖에서 접근을 할 수 없으므로, 편리한 사용을 위해 쉘이나 터미널환경으로 접속을 할 수 있게 해줌.

ex) docker exec -it 컨테이너id sh/bash/zsh/powershell
				bin/bash

터미널 환경에서는 ctrl+d 로 빠져나올 수 있음.

//컨테이너간 통신

같은 도커 호스트
컨테이너는 프로세스이며, 다음 실행시에 동일한 ip의 할당이 보장되지 않으므로 ip를 이용한 통신은 비효율적.
:: 도커의 컨테이너 링크 link를 활용하여 컨테이너명을 통해 통신이 가능하다.

docker run -link

다른 도커 호스트
다른도커 호스트간에 link기능은 작동하지 ㅇ낳아 container ochestration도구를 사용하는게 좋다.
k8s
docker swam
apache mesos

./..
보통 도커이미지는 응용 소프트웨어별로 생성.
서로간 통신이 많아지고 데이터를 주고받아야함.
link >>같은 호스트에 있는 컨테이너들끼리 통신할 수 있는 방버중 하나.
:: 내부 도커 네트워크를 통해 이뤄짐. >
:: 통신은 호스트의 네트워크로는 노출되지 않음을 의미.

--link CONTAINER:ALIAS
CONTAINER 는 링크컨테이너(연결되는 컨테이너)
ALIAS는 마스터 컨테이너(시작되는 컨테이너)

컨테이너가 링크되면 도커는 IPTABLES규칙을 설정 후 명시적 설정된 포트를 통해 컨테이너가 통신.

컨테이너 재시작시에 그대로 유지되지 않음.
마스터 컨테이너 시작되기전 링크 컨테이너 시작이 필요.
양방향 링크는 불가능.



///포트지정 -P


///계층, 이미지제어
각 Docker이미지 파일은 일련의 계층으로 이루어지며, 이들은 하나의 단일 이미지로 결합함.
이미지 변경시마다 계층이 생성.
사용자의 실행/복사와 같은 명령시에 새 계층이 생성됨.
Docker는 이런 계층을 재사용하기에 구축 프로세스가 훨씬 더 빨라짐.
이미지 사이에서 중간 변경사항이 공유되어 속도/규모/효율성이 개선됨.
계층화에서는 버전관리가 내재되어있으며, 변경사항 발생시 내장 변경 로그가 기본적으로 적용.
>> 컨테이너 이미지를 완전히 제어.
계층적으로 관리되므로 필요시 롤백이 가능.

각프로세스에 대한 컨테이너를 생성함으로, 유사한 프로세스를 빠르게 공유가 가능함.
OS부팅이 필요없어 배포시간이 크게 단축됨.
배포 속도가 빨라 컨테이너에서 생성된 데이터를 비용 효율적으로 쉽게 생성/삭제가 가능.
>> 효율성을 중시한, 세분화되고 제어 가능한 마이크로서비스 기반 접근 방식

! 수백개의 세분화된 컨테이너를 관리하는것은 매우 어려워질 수 있게됨.
도커는 기본적으로 단일 컨테이너 관리에 적합
결론적으론 네트워킹, 보안, 텔레메트리와 같은 서비스를 그룹화할 필요가 잇음.
>> 쿠버네티스의 필요성.


//호스트와 컨테이너간 파일 전송 바업
1) 호스트에서 컨테이너로
docker cp /path/foo.txt mycontainer:/path/foo.txt
2) 컨테이너에서 호스트로
docker cp mycontainer:/path/foo.txt /path/foo.txt


> ## 참고링크
> https://velog.io/@how0326/%EB%8F%84%EC%BB%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88
> https://velog.io/@yangju0411/%EC%A7%A7-%EB%8F%84%EC%BB%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EA%B0%84-%ED%86%B5%EC%8B%A0