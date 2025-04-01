## 서비스별 개요

###  **gateway-service**

> 클라이언트의 모든 요청을 받아 내부 마이크로서비스로 라우팅 해주는 API Gateway 역할을 합니다.
> 
> 
> 요청 라우팅, 인증 필터(JWT), 응답 처리 등의 역할을 하며, Eureka와 연동하여 동적으로 서비스 인스턴스를 탐색합니다.
> 
> 게이트웨이를 통해 외부에 단일 엔드포인트를 제공하고 내부 마이크로서비스 구조를 감춥니다.
> 

---

###  **eureka-service**

> Spring Cloud Netflix Eureka 서버로, 마이크로서비스들의 서비스 디스커버리 기능을 제공하는 중앙 레지스트리 서버입니다.
> 
> 
> 각 서비스는 Eureka에 자신을 등록하고, 다른 서비스의 위치 정보를 Eureka를 통해 조회합니다.
> 

---

###  **config-service**

> Git 저장소에 있는 각 마이크로서비스의 설정 파일을 중앙에서 관리해주는 설정 서버입니다.
> 
> 
> Config Server는 **Git 저장소**에서 설정 파일들을 읽어와서 제공합니다.
> 
> 마이크로서비스들은 애플리케이션 부트스트랩 시 Config Server로부터 자신에 해당하는 설정 정보를 가져옵니다.


### Outer-Architecture (EC2 + Docker Compose 기반)

- `eureka-service`, `config-service`, `gateway-service`는 EC2 서버 내에서 도커 컴포즈를 통해 실행됩니다.
    
    [docker-compose.yaml](https://github.com/LGCoreNova/Kitcha-BE/blob/main/docker-compose.yaml)
    
- 서버에는 `.env` 파일을 두어 필요한 환경변수를 구성합니다.

```bash
# EC2 접속 후
cd ~/kitcha/outer-architecture
docker-compose --env-file .env up -d
```

- `.env` 예시:

```
FRONTEND_SERVER=example-frontend-server-url
TOKEN_SECRET=example-token-secret
```

---

>
