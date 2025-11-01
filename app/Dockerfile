# 1단계: 빌더 이미지 (wheel 빌드용)
FROM python:3.12-slim AS builder

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y \
    build-essential \
    default-libmysqlclient-dev \
    pkg-config \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /code

COPY requirements.txt .
RUN pip install --upgrade pip && pip wheel --no-cache-dir --no-deps -r requirements.txt -w /wheels


# 2단계: 실제 런타임 이미지 (더 얇음)
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y \
    default-libmysqlclient-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /code

# 의존성 설치
COPY --from=builder /wheels /wheels
RUN pip install --no-cache /wheels/*

# 프로젝트 복사
COPY . /code

# static / media 디렉토리 보장
RUN mkdir -p /code/static /code/media

EXPOSE 8000

# 컨테이너 기본 커맨드는 docker-compose에서 gunicorn으로 override할 거라 여기선 안 적어도 됨
