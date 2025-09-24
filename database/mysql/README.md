# MySQL (Kustomize-based)

Kubernetes 환경에서 MySQL을 Kustomize 구조로 배포하기 위한 설정입니다.  
StatefulSet 기반으로 구성되어 있으며, 사용자 계정 및 초기화 SQL 파일은 Overlay를 통해 설정할 수 있습니다.

---

## 📁 Directory Structure
```sh
mysql
├── base
│   ├── configmap.yaml           # 초기화 SQL을 포함한 ConfigMap
│   ├── secret.yaml              # 기본 secret 템플릿 (Patch 대상)
│   ├── service.yaml             # Headless Service
│   └── statefulset.yaml         # StatefulSet 정의
└── overlays
    └── kustomization.yaml       # 환경별 overlay 구성
```
---
## ⚙️ 커스터마이징 방법
### 1. 사용자 이름 및 비밀번호 변경
`overlays/kustomization.yaml`에서 다음 항목을 수정하세요:
```yaml
patches:
  - target:
      kind: Secret
      name: mysql-secret
    patch: |
      - op: replace
        path: /stringData
        value:
          MYSQL_USER: myuser
          MYSQL_PASSWORD: mypass123
          MYSQL_DATABASE: mydb
```
### 2. MySQL 버전 변경
Docker Hub에서 제공하는 공식 MySQL 이미지를 사용하며, 동일 파일 내 `images:` 항목의 `newTag` 값을 원하는 MySQL 버전으로 바꿔주세요:
```yaml
images:
  - name: mysql
    newTag: 8.0 # 원하는 버전으로 수정 가능 (예시: 8.0)
```
---
## 🗂️ Init SQL 설정
초기화 SQL 파일은 `base/init.sql` 파일입니다.  
MySQL 컨테이너 시작 시 `/docker-entrypoint-initdb.d/init.sql`에 마운트되어 **최초 1회만 실행**됩니다
```yaml
configMapGenerator:
- name: sql-init-script
  files:
  - init.sql=init.sql  # 상대경로로 init.sql 지정
```
> ✅ SQL 내용을 바꾸려면 base/init.sql을 수정하거나, 다른 파일 경로를 지정하세요.
---

## 📝 주의사항
- StatefulSet 기반으로 구성되어 있어, Pod가 삭제되더라도 데이터는 유지됩니다.
- 데이터를 초기화하거나 삭제하고 싶을 경우 다음 PVC 삭제 명령어를 사용할 수 있습니다:
```sh
kubectl delete pvc -l app=mysql -n <goormthon-n>
```
- ❗ 이 명령은 데이터를 완전히 삭제합니다. 신중히 실행하세요.
---
## 📦 배포 방법
1. `overlays/kustomization.yaml` 파일에서 `<goormthon-n>`을 현재 팀 네임스페이스로 변경합니다.
2. 아래 명령어로 배포합니다:
```sh
kubectl apply -k database/mysql/overlays
```
---
## 📌 기타 참고
- base 디렉토리의 `configmap.yaml` 파일은 init.sql을 포함하고 있습니다.
- 사용자 계정, DB 이름, 초기 SQL 등은 overlay에서 Patch 방식으로 수정 가능합니다.
- 배포 전 실제 배포 리소스를 확인하려면 다음 명령어를 사용하세요:
    ```sh
    kustomize build database/mysql/overlays
    ``` 
