# ====================================
# Flask 애플리케이션 Docker 이미지 빌드
# ====================================
# 이 Dockerfile은 Flask 웹 애플리케이션을 Docker 컨테이너에서 실행하기 위한
# 이미지를 생성합니다. MySQL 데이터베이스와 함께 작동합니다.

# Step 1: Use a lightweight Python base image
# python:3.13-slim은 전체 Python 이미지보다 크기가 작아 컨테이너 이미지 최적화에 유리함
FROM python:3.13-slim

# Step 2: Set the working directory inside the container
# 컨테이너 내부에서 모든 명령이 실행될 기본 디렉토리 설정
WORKDIR /app

# Step 3: Copy the application code into the container
# 호스트의 현재 디렉토리의 모든 파일을 컨테이너의 /app 디렉토리로 복사
# (app.py, requirements.txt 등이 포함됨)
COPY . /app

# Step 4: Install Python dependencies
# requirements.txt에 정의된 패키지들을 설치
# --no-cache-dir 옵션으로 캐시를 저장하지 않아 이미지 크기 감소
RUN pip install --no-cache-dir -r requirements.txt

# Step 5: Expose the port the application will run on
# 포트 5000을 외부에 노출 (Flask는 기본적으로 5000 포트에서 실행)
# 이 포트는 docker-compose.yml에서 호스트 포트와 매핑됨
EXPOSE 5000

# Step 6: Define the default command to run the application
# 컨테이너가 시작될 때 자동으로 실행될 명령어
# Flask 애플리케이션(app.py)을 Python 인터프리터로 실행
CMD ["python", "app.py"]
