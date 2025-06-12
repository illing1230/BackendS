# Kubernetes & Docker 학습 가이드

## 개요
Kubernetes와 Docker를 함께 사용하여 컨테이너를 관리하기 위한 학습 로드맵입니다.

## 필요한 핵심 개념들

### Docker 기초 개념

#### 컨테이너화 기본 이해
- Dockerfile 작성법
- 이미지 빌드
- 컨테이너 실행
- 네트워킹
- 볼륨 마운트
- 멀티스테이지 빌드
- 이미지 최적화 기법

#### 레지스트리 관리
- Docker Hub, ECR, GCR 사용법
- 이미지 푸시/풀
- 태깅 전략
- 보안 스캔

### Kubernetes 핵심 개념

#### 기본 오브젝트들
- **Pod**: 컨테이너를 실행하는 가장 작은 단위
- **Deployment**: Pod의 배포와 업데이트 관리
- **Service**: Pod 간 통신과 로드 밸런싱
- **ConfigMap**: 설정 데이터 관리
- **Secret**: 민감한 데이터 관리
- **Namespace**: 리소스 격리

#### 네트워킹
- 클러스터 내부 통신
- 서비스 디스커버리
- 인그레스 컨트롤러
- 네트워크 정책

#### 스토리지
- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)
- StorageClass
- 상태가 있는 애플리케이션 관리

### 실제 운영을 위한 고급 개념

#### 리소스 관리
- CPU, 메모리 requests/limits 설정
- HPA (Horizontal Pod Autoscaler)
- VPA (Vertical Pod Autoscaler)

#### 보안
- RBAC (Role-Based Access Control)
- SecurityContext
- Network Policies
- Pod Security Standards

#### 모니터링과 로깅
- Prometheus
- Grafana
- ELK 스택

## 추천 학습 순서

1. **Docker 기초** - 컨테이너 개념부터 Dockerfile 작성까지
2. **Kubernetes 아키텍처 이해** - 마스터 노드, 워커 노드, etcd 등
3. **기본 오브젝트 실습** - kubectl로 Pod, Deployment 등 생성/관리
4. **네트워킹과 서비스 디스커버리**
5. **스토리지와 상태 관리**
6. **헬름(Helm) 패키지 매니저**
7. **운영 도구들** - 모니터링, 로깅, CI/CD

## 1단계: Docker 기초 상세 학습

### 컨테이너 개념 이해

#### 컨테이너란?
- 가상머신과 달리 OS 커널을 공유
- 애플리케이션을 격리된 환경에서 실행
- 가볍고 빠르며 일관된 실행 환경 제공

#### Docker 핵심 구성요소
- **이미지(Image)**: 컨테이너 실행에 필요한 파일과 설정을 담은 템플릿
- **컨테이너(Container)**: 이미지를 실행한 인스턴스
- **레지스트리(Registry)**: 이미지를 저장하고 공유하는 저장소

### Docker 기본 명령어

```bash
# 이미지 다운로드
docker pull nginx

# 컨테이너 실행
docker run -d -p 8080:80 --name my-nginx nginx

# 실행 중인 컨테이너 확인
docker ps

# 컨테이너 로그 확인
docker logs my-nginx

# 컨테이너 내부 접속
docker exec -it my-nginx bash

# 컨테이너 중지/시작/삭제
docker stop my-nginx
docker start my-nginx
docker rm my-nginx
```

### Dockerfile 작성 기초

#### 기본 Node.js 애플리케이션 예시
```dockerfile
# 베이스 이미지 선택
FROM node:18-alpine

# 작업 디렉토리 설정
WORKDIR /app

# 의존성 파일 복사
COPY package*.json ./

# 의존성 설치
RUN npm install

# 소스 코드 복사
COPY . .

# 포트 노출
EXPOSE 3000

# 실행 명령
CMD ["npm", "start"]
```

## Python & JavaScript 실습 예제

### Python Flask 애플리케이션

#### app.py
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello from Python Flask in Docker!'

@app.route('/health')
def health():
    return {'status': 'healthy'}

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

#### requirements.txt
```
Flask==2.3.3
```

#### Dockerfile
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# 의존성 파일 먼저 복사 (캐시 최적화)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 소스 코드 복사
COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### Node.js Express 애플리케이션

#### app.js
```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
    res.json({ message: 'Hello from Node.js Express in Docker!' });
});

app.get('/health', (req, res) => {
    res.json({ status: 'healthy' });
});

app.listen(port, '0.0.0.0', () => {
    console.log(`Server running on port ${port}`);
});
```

#### package.json
```json
{
  "name": "docker-node-app",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

#### Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# package.json 먼저 복사 (캐시 최적화)
COPY package*.json ./
RUN npm install

# 소스 코드 복사
COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

## 실습 단계별 진행

### 1단계: 각각 빌드하고 실행하기

```bash
# Python 앱
cd python-app
docker build -t my-python-app .
docker run -p 5000:5000 my-python-app

# Node.js 앱
cd node-app  
docker build -t my-node-app .
docker run -p 3000:3000 my-node-app
```

### 2단계: 환경변수 사용하기

#### Python Flask 앱에 환경변수 추가
```python
import os
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    env = os.getenv('ENVIRONMENT', 'development')
    return f'Hello from {env} environment!'
```

#### 실행
```bash
docker run -p 5000:5000 -e ENVIRONMENT=production my-python-app
```

### 3단계: 볼륨 마운트로 개발 환경 구성

```bash
# 소스 코드 변경이 실시간 반영되도록
docker run -p 5000:5000 -v $(pwd):/app my-python-app
```

### 4단계: Docker Compose로 두 앱 함께 실행

#### docker-compose.yml
```yaml
version: '3.8'
services:
  python-app:
    build: ./python-app
    ports:
      - "5000:5000"
    environment:
      - ENVIRONMENT=docker-compose
    
  node-app:
    build: ./node-app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
```

### 5단계: 데이터베이스 연결하기

#### Python 앱에 PostgreSQL 연결 추가
```python
import psycopg2
import os

@app.route('/db')
def test_db():
    try:
        conn = psycopg2.connect(
            host=os.getenv('DB_HOST', 'localhost'),
            database=os.getenv('DB_NAME', 'testdb'),
            user=os.getenv('DB_USER', 'user'),
            password=os.getenv('DB_PASSWORD', 'password')
        )
        return 'Database connected!'
    except Exception as e:
        return f'Database error: {str(e)}'
```

#### Docker Compose에 PostgreSQL 추가
```yaml
services:
  python-app:
    # ... 기존 설정
    depends_on:
      - db
    environment:
      - DB_HOST=db
      - DB_NAME=testdb
      - DB_USER=user
      - DB_PASSWORD=password
  
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=testdb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## 추천 실습 순서

### 기본 실습
1. Docker 설치 후 `docker run hello-world` 실행
2. nginx 컨테이너로 웹서버 띄워보기
3. 간단한 Python/Node.js 앱 도커라이즈하기
4. 데이터베이스 컨테이너(MySQL, PostgreSQL) 실행해보기
5. docker-compose로 멀티 컨테이너 앱 구성하기

### 주간 실습 계획
- **Day 1-2**: 각 언어별 기본 앱 도커라이즈
- **Day 3-4**: 환경변수, 볼륨 마운트 실습
- **Day 5-6**: Docker Compose로 멀티 컨테이너 구성
- **Day 7**: 데이터베이스 연결 및 네트워킹

## 실습 환경 설정

### 로컬 개발 환경
- **minikube**: 로컬 Kubernetes 클러스터
- **kind**: Docker 컨테이너 안에서 실행되는 Kubernetes

### 클라우드 관리형 서비스
- **EKS** (Amazon Elastic Kubernetes Service)
- **GKE** (Google Kubernetes Engine)
- **AKS** (Azure Kubernetes Service)

## 다음 단계

Docker 기초를 마스터한 후에는 Kubernetes 아키텍처 이해로 넘어가며, 점진적으로 복잡한 운영 시나리오를 다루게 됩니다.
