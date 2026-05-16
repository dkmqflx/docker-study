# Step 1: Specify the base image
# Use an official lightweight image, such as Python, for simplicity

# python:3.13-slim = Python 3.13 설치된 경량 이미지 (불필요한 패키지 제거로 용량 최소화)
# 다른 옵션: python:3.13 (더 많은 패키지 포함, 크기 커짐)
FROM python:3.13-slim

# Step 2: Set the working directory inside the container
# This is where all commands and files will be located during build

# 컨테이너 내부에 /app 디렉토리를 생성하고, 이를 기본 작업 디렉토리로 설정
# 이후의 모든 RUN, COPY, CMD 등의 명령어는 /app을 기준으로 실행됨
WORKDIR /app

# Step 3: Copy the application code into the container
# "." represents the current directory on the host, and "/app" is the container's directory
# 호스트(개발 컴퓨터)의 현재 디렉토리 모든 파일을 컨테이너의 /app 디렉토리로 복사
# .dockerignore 파일에 명시된 파일/디렉토리는 제외됨 (예: .git, __pycache__, *.pyc)
# 여기에서는 app.py, requirements.txt, README.md 파일이 복사됨
COPY . /app

# Step 4: Install application dependencies
# Use a requirements.txt file for Python dependencies

# pip install: Python 패키지 설치 명령어
# -r requirements.txt: requirements.txt 파일에 명시된 모든 패키지 설치
# --no-cache-dir: pip 캐시를 저장하지 않아 컨테이너 이미지 크기 감소
RUN pip install --no-cache-dir -r requirements.txt

# Step 5: Expose a port
# Inform Docker which port the application will listen on

# EXPOSE는 문서화 목적이며, 실제로 포트를 열지는 않음
# docker run 시 -p 5000:5000 옵션으로 호스트와 컨테이너의 포트를 연결해야 함
EXPOSE 5000

# Step 6: Specify the default command to run the application
# Replace `app.py` with the name of your main Python script

# CMD: 컨테이너가 시작될 때 기본으로 실행할 명령어 지정
# ["python", "app.py"]는 JSON 형식 (권장)
# docker run 시 다른 명령어로 덮어쓸 수 있음: docker run <image> python other_script.py
CMD ["python", "app.py"]

