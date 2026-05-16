## 05. Docker CLI(Command Line Interface) - 명령어

- **Container Management**에 있는 명령어 잘 안씀

- `docker attach [container-id/name]` 명령어 거의 안씀

- `13 docker kill [options] [container-id/name]` 명령어 많이 씀

## 06. Docker CLI 실습

### docker pull nginx

Docker Hub에서 nginx(웹 서버) 이미지를 다운로드하는 명령어

```bash
# Docker Hub에서 nginx 이미지를 로컬 컴퓨터에 다운로드
# pull = 받아오다(다운로드)
# nginx = 다운로드할 이미지 이름
docker pull nginx

docker images # 이미지 목록 확인

docker run nginx # nginx 이미지를 실행


```

**설명:**

- 첫 번째 docker pull 시: 네트워크에서 이미지를 다운로드
- 다음 docker pull 시: 이미 로컬에 있으면 "Already exists" 메시지 표시
- 다운로드 후 `docker images` 명령어로 확인 가능

- docker hub에서 확인이 가능하다

  - https://hub.docker.com/search?q=nginx

```bash

docker ps # 실행중인 컨테이너 목록 확인 - nginx 컨테이너 실행 확인

docker logs <container-id> # 컨테이너 로그 확인

# ctrl + c 를 누르면 컨테이너 종료


docker rmi <image-id> # 이미지 삭제



```

```bash

docker ps # 실행중인 컨테이너 목록 확인 - nginx 컨테이너 실행 확인

docker exec -it <container-id> bash # 컨테이너 내부에 접속

ls # 컨테이너 내부에서 파일 목록 확인, root@<container-id>:/ # 컨테이너 내부에서 파일 목록 확인

exit # 컨테이너 내부에서 나오기

docker stop <container-id> # 컨테이너 우아한 종료

docker kill <container-id> # 컨테이너 강제 종료


```

### docker stop vs docker kill

| 명령              | 신호    | 동작                                                                                                                |
| ----------------- | ------- | ------------------------------------------------------------------------------------------------------------------- |
| **`docker stop`** | SIGTERM | 우아한 종료(graceful shutdown). 컨테이너가 신호를 받으면 정리 작업을 한 후 종료할 기회를 가짐. 기본 타임아웃은 10초 |
| **`docker kill`** | SIGKILL | 즉각적인 종료. 신호를 무시할 수 없으며 컨테이너가 즉시 중단됨                                                       |

**차이:**

- `docker stop`: 컨테이너에게 "Please shut down gracefully" 요청 → 10초 대기 → 그래도 살아있으면 SIGKILL 전송
- `docker kill`: 컨테이너에게 "Stop now!" 강제 명령 → 즉시 중단

**사용 시기:**

- **`docker stop`**: 일반적으로 사용. DB 같은 상태를 저장해야 하는 앱에 권장
- **`docker kill`**: 응답하지 않는 컨테이너를 강제로 종료해야 할 때

```bash

docker run -d nginx # 백그라운드에서 nginx 컨테이너 실행

```

## 07. 나의 첫번째 Docker image 만들어보기(ft. Flask)

### Dockerfile 이해하기 - 이미지와 환경 설정

**질문**: pip install을 한 건 app을 실행시키기 위한 라이브러리를 설치한 건가?

**답변**: 맞습니다! pip install은 **app.py가 필요로 하는 외부 라이브러리들을 설치**하는 것입니다.

#### 실제 예시

`requirements.txt`에 다음과 같이 되어 있다면:

```
Flask==2.3.0
requests==2.31.0
numpy==1.24.0
```

`RUN pip install -r requirements.txt` 명령어는:

- Flask (웹 프레임워크)
- requests (HTTP 요청 라이브러리)
- numpy (수치 계산 라이브러리)

이 라이브러리들을 설치합니다. 이들이 미리 설치되어야 app.py에서 `from flask import Flask`처럼 import할 수 있습니다.

#### 설치 주체별 정리

| 설치 주체           | 설치 내용                         | 명령어                                |
| ------------------- | --------------------------------- | ------------------------------------- |
| **FROM**            | Python 3.13 (기본 환경)           | `FROM python:3.13-slim`               |
| **RUN pip install** | app.py가 필요한 외부 라이브러리들 | `RUN pip install -r requirements.txt` |
| **COPY**            | 너의 app.py 소스코드              | `COPY . /app`                         |

이 세 가지가 다 있어야 `CMD ["python", "app.py"]`가 정상 실행됩니다.

```bash

cd my-first-docker

docker build -t my-first-docker . # Dockerfile 이용해서 이미지 빌드 -t 옵션은 이미지 이름 지정

docker images # 이미지 목록 확인

docker run -p 5001:5000 my-first-docker # 이미지 실행 -p 옵션은 호스트와 컨테이너의 포트를 연결

# http://0.0.0.0:5001/ 접속하면 "Welcome to the Docker Lecture App! Thank you" 메시지 표시


# 만약 app.py 의 Welcome 메시지를 수정 후 다시 실행하고 싶다면?

docker build -t my-first-docker . # Dockerfile 이용해서 이미지 빌드 -t 옵션은 이미지 이름 지정

docker run -p 5001:5000 my-first-docker # 이미지 실행 -p 옵션은 호스트와 컨테이너의 포트를 연결

# http://0.0.0.0:5001/ 접속하면 "Welcome to the Docker Lecture App! Thank you2" 메시지 표시


```

#### -p 옵션(포트 포워딩) 이해하기

`-p 5001:5000`의 의미:

| 구성요소 | 설명                               |
| -------- | ---------------------------------- |
| **5001** | 호스트(당신의 컴퓨터) 포트 번호    |
| **5000** | 컨테이너(Docker 내부) 포트 번호    |
| **역할** | 호스트:5001 ← 연결 → 컨테이너:5000 |

**동작 원리:**

1. 당신의 브라우저에서 `localhost:5001`로 접속
2. 호스트 포트 5001로 요청 도착
3. 자동으로 컨테이너 내부의 포트 5000으로 전달
4. 컨테이너 안의 Flask 앱이 포트 5000에서 응답
5. 응답이 다시 호스트로 돌아옴

**누가 정하는가?**

- **호스트 포트(5001)**: 당신이 자유롭게 정함 (원하면 8080, 3000 등으로 변경 가능)
- **컨테이너 포트(5000)**: 이미지 안의 애플리케이션이 미리 정함 (Flask 코드에서 `port=5000`으로 설정됨)

**다양한 예시:**

```bash
docker run -p 8080:5000 my-first-docker    # 호스트 8080 ← 컨테이너 5000
docker run -p 3000:5000 my-first-docker    # 호스트 3000 ← 컨테이너 5000
docker run -p 5000:5000 my-first-docker    # 호스트 5000 ← 컨테이너 5000 (같은 포트)
```

#### Dockerfile의 EXPOSE와 포트의 관계

**EXPOSE 5000의 의미:**

| 항목            | 설명                                                                 |
| --------------- | -------------------------------------------------------------------- |
| **EXPOSE 5000** | "우리 앱은 포트 5000을 사용합니다"라고 **선언**하는 것 (문서화 목적) |
| **실제 역할**   | 실제로 포트를 여는 것이 아니며, 아무것도 하지 않음                   |

**포트 5000이 사용되는 진짜 원인:**

1. **app.py (Flask 앱)**: 포트 5000으로 설정되어 있음

   ```python
   app.run(port=5000)  # Flask가 5000에서 실행됨
   ```

2. **Dockerfile의 EXPOSE 5000**: 위의 사실을 선언함

   ```dockerfile
   EXPOSE 5000  # "이 앱은 5000을 사용합니다"라고 문서화
   ```

3. **docker run -p 5001:5000**: EXPOSE에 선언된 포트를 호스트와 연결
   ```bash
   docker run -p 5001:5000 my-first-docker
   ```

**정리:**

```
원인: app.py가 포트 5000으로 설정됨
  ↓
선언: Dockerfile의 EXPOSE 5000에서 명시
  ↓
연결: docker run -p 5001:5000에서 호스트와 연결
```

**따라서:**

- **5000은 app.py가 정한 것** (컨테이너 포트)
- **EXPOSE는 그것을 선언하는 것** (문서화)
- **5001은 당신이 정하는 것** (호스트 포트)
