<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 README.md 파일에 작성하여 PR을 보내주세요.

### 0단계 - pem 키 생성하기

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)에 업로드해주세요

2. 업로드한 pem키는 무엇인가요.
- ychxexn.pem

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 172.31.30.0/26

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL : http://subway.wtcpro-ychxexn.o-r.kr/



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://proxy.wtcpro-ychxexn.o-r.kr/

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.
```
#!/bin/bash
## 파라미터 체크
if [ $# -ne 2 ]; then
        echo "> ./deploy.sh [브랜치명] [환경] 형태의 요청만 가능합니다."
        exit 0
fi

## 변수 설정
PROJECT=/home/ubuntu/nextstep/infra-subway-deploy
BRANCH=$0
PROFILE=$1
txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray

echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

## 저장소 pull
cd $PROJECT
git fetch
master=$(git rev-parse $BRANCH)
remote=$(git rev-parse origin $BRANCH)

echo -e "${txtylw}=======================================${txtrst}"
if [[ $master == $remote ]]; then
    echo -e "[$(date)] 변경 사항이 없어요 😫"
    exit 0
else
    echo -e "${txtgrn}  << git pull 🧐 >>${txtrst}"
    git pull
fi
echo -e "${txtylw}=======================================${txtrst}"

## gradle build
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  << build... 🧐 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"
./gradlew build
              
## 프로세스 pid를 찾는 명령어
CURRENT_PID=$(pidof java)
echo "> 현재 구동중인 애플리케이션 pid: $CURRENT_PID"

## 프로세스를 종료하는 명령어
if [ -z "$CURRENT_PID" ]; then
        echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다."
else
        echo "> kill -15 $CURRENT_PID"
        kill -15 $CURRENT_PID
        sleep 5
fi

# 프로세스 실행
nohup java -jar -Dspring.profiles.active=$PROFILE ./build/libs/subway-0.0.1-SNAPSHOT.jar 1> subway.log 2>&1 &

echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  << 실행 완료 🥳 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"                                   
```
