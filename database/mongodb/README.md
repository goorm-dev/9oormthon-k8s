# MongoDB (Kustomize Deployment)

Kubernetes 환경에서 MongoDB를 Kustomize 구조로 배포하기 위한 설정입니다.  
StatefulSet 기반으로 구성되어 있으며, 사용자 계정과 초기 Database 설정은 Kustomize Overlay를 통해 쉽게 구성할 수 있습니다.

---

## 📁 Directory Structure
```sh
├── mongodb
│   ├── base
│   │   ├── create_collction
│   │   │   └── job.yaml
│   │   ├── kustomization.yaml
│   │   ├── secret.yaml
│   │   ├── service.yaml
│   │   └── statefulset.yaml
│   └── overlays
│       └── kustomization.yaml
```
---

## ⚙️ 커스터마이징 방법

### 1. 사용자 이름, 비밀번호 및 Database 이름 설정

`overlays/kustomization.yaml`에서 다음 항목을 수정하세요:

```yaml
patches:
  - target: 
      kind: Secret
      name: mongodb-secret
    patch: |-
      - op: replace
        path: /stringData
        value:
          MONGO_INITDB_ROOT_USERNAME: myuser
          MONGO_INITDB_ROOT_PASSWORD: mypassword
          MONGO_INITDB_DATABASE: myapp
```
---
### 2. MongoDB 클러스터 내부 호스트 주소
MongoDB는 StatefulSet으로 구성되어 있으므로, 아래와 같은 내부 DNS 주소를 통해 Pod에 접근할 수 있습니다:
```
mongodb-0.mongodb.<goormthon-n>.svc.cluster.local:27017
```
- `mongodb-0`: MongoDB Pod 이름
- `mongodb`: MongoDB 서비스 이름
- `<goormthon-n>`: MongoDB를 배포한 네임스페이스 이름 <예시: goorm>
- `svc.cluster.local`: Kubernetes 클러스터 내부 DNS 도메인
    - 이 주소는 MongoDB 내부 명령어, Job 등을 통해 collection 생성 시에도 사용됩니다.
예시:
```js
mongosh "mongodb://myuser:mypassword@mongodb-0.mongodb.<goormthon-n>.svc.cluster.local:27017/admin"
```
---
## 🛠️ (선택) 데이터 커스텀 Job

컬렉션 생성, 삭제 등 데이터 커스텀이 필요한 경우 `base/create_collction/job.yaml` 파일을 활용할 수 있습니다.  
필요 시 `overlays/kustomization.yaml`에 아래 주석을 해제하고 환경에 맞게 수정하여 사용하세요:

```yaml
# - target:
#     kind: Job
#     name: mongosh
#   patch: |-
#     apiVersion: batch/v1
#     kind: Job
#     metadata:
#       name: mongosh
#     spec:
#       template:
#         spec:
#           containers:
#           - name: mongosh
#             command:
#             - /bin/bash
#             - -c
#             - |
#               mongosh "mongodb://$(MONGO_USERNAME):$(MONGO_PASSWORD)@mongodb-0.mongodb.<goormthon-n>.svc.cluster.local:27017/admin" \
#               --eval 'db = db.getSiblingDB("myapp"); db.createCollection("init_check"); print("✅ myapp DB and collection created.");'
```
> - 📝 이 Job은 DB 생성과 직접적인 관계는 없습니다. 운영 중 필요한 작업에만 사용하세요.  
> - ❗ Job 실행 시, MongoDB가 이미 실행 중이어야 합니다. Pod가 준비되기 전에 Job이 실행되면 실패할 수 있습니다.
> - ❗ host 주소는 위에서 설명한 내부 DNS 주소를 사용해야 합니다.
---
## 📦 배포 방법
```sh
kubectl apply -k database/mongodb/overlays
```
---
## 📌 기타 참고
- base는 overlay에서 상속되어 재사용됩니다.
- secret, service, statefulset 등은 overlays에서 patch 방식으로 수정할 수 있습니다.
- DB 명, 사용자 계정, 버전 등의 설정은 overlays/kustomization.yaml에서 조절하세요.
- 수정 전 변경 내용을 미리 확인하려면 다음 명령어를 사용할 수 있습니다:
```sh
kustomize build database/mongodb/overlays
```
> - [MongoDB 공식 문서](https://www.mongodb.com/docs/manual/)
> - [Kubernetes 공식 문서](https://kubernetes.io/docs/home/)
> - [Kustomize 공식 문서](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
