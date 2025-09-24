# Redis (Kustomize-based)

Kubernetes 환경에서 Redis를 Kustomize 구조로 배포하기 위한 설정입니다.  
StatefulSet 기반으로 구성되어 있으며, 클러스터 내부에서 안정적인 Redis 서비스 구성을 목표로 합니다.

---

## 📁 Directory Structure

```sh
redis
├── README.md
└── base
    ├── kustomization.yaml     # base Kustomization 정의
    ├── service.yaml           # Headless Service 정의
    └── statefulset.yaml       # StatefulSet 정의
```
## ⚙️ 커스터마이징 방법
### 1. Redis 버전 변경
`base/kustomization.yaml`에서 다음 항목의 `newTag`를 원하는 Redis 버전으로 수정하세요.
```yaml
images:
  - name: redis
    newTag: 7.2 # 원하는 Redis 버전으로 변경
```
Redis의 공식 Docker 이미지에서 제공하는 태그를 사용할 수 있습니다:
> 🔗 https://hub.docker.com/_/redis

### 3. 네임스페이스 설정
`base/kustomization.yaml`의 `namespace:` 항목을 현재 배포하려는 팀 네임스페이스로 변경하세요.

```yaml
namespace: <goormthon-n> # 현재 팀 네임스페이스로 변경 <예: goormthon-1>
```
---
## 📝 주의사항

- 본 배포는 StatefulSet 기반으로 구성되어 있으므로, Pod가 삭제되더라도 Redis 데이터는 유지됩니다.

- 데이터를 완전히 삭제하고 싶을 경우 PVC 삭제가 필요합니다
```yaml
kubectl delete pvc -l app=redis -n <goormthon-n>
```
❗ 이 명령은 Redis의 모든 데이터를 삭제하므로 주의가 필요합니다.

- 클러스터 내부에서 Redis에 접근하려면 다음과 같은 주소를 사용할 수 있습니다.
```
redis-0.redis.<goormthon-n>.svc.cluster.local:6379
```
> 이는 Redis Pod의 DNS 주소이며, Kubernetes Cluster 내부에서만 접근 가능합니다.
---
## 📦 배포 방법
1. `base/kustomization.yaml` 파일에서 `<goormthon-n>`을 실제 네임스페이스로 바꿉니다.

2. 아래 명령어로 배포합니다
```sh
kubectl apply -k database/redis/base
```
---
## 📌 기타 참고
- 별도의 Overlay 구성 없이 base 디렉토리만으로도 단일 Redis 인스턴스를 배포할 수 있습니다.

- StatefulSet은 데이터를 `/data` 경로에 저장하며, 기본 Redis 설정이 사용됩니다.

- 필요 시 ConfigMap이나 Secret 리소스를 추가해 Redis 설정을 확장할 수 있습니다.