# MariaDB (Kustomize Deployment)

Kubernetes 환경에서 MariaDB를 Kustomize 구조로 배포하기 위한 설정입니다.  
StatefulSet 기반으로 구성되어 있으며, 초기화 SQL 스크립트와 사용자 계정은 Kustomize Overlay를 통해 손쉽게 설정할 수 있습니다.

---

## 📁 Directory Structure
```sh
.
└── database
    └── mariadb
        ├── README.md
        ├── base
        │   ├── init.sql              # 초기 SQL 스크립트
        │   ├── kustomization.yaml    # base Kustomization 정의
        │   ├── secret.yaml           # 기본 secret 템플릿 (Patch 대상)
        │   ├── service.yaml          # Headless Service
        │   └── statefulset.yaml      # StatefulSet 정의
        └── overlays
            └── kustomization.yaml    # 환경별 overlay 구성
```
---

## ⚙️ 커스터마이징 방법

### 1. 사용자 이름 및 비밀번호 변경

`overlays/kustomization.yaml`에서 다음 항목을 수정하세요:

```yaml
patches:
  - target:
      kind: Secret
      name: mariadb-secret
    patch: |
      - op: replace
        path: /stringData
        value:
          MYSQL_USER: myuser
          MYSQL_PASSWORD: mypass123
          MYSQL_DATABASE: mydb
```
### 2. MariaDB 버전 변경

동일 파일 내 images: 항목의 newTag 값을 원하는 MariaDB 버전으로 바꿔주세요:
```yaml
images:
  - name: mariadb
    newTag: lts-ubi # 원하는 버전으로 수정 가능 (예시: v11.3)
```
---
### 3. init.sql 실행
초기화 SQL 파일은 `base/init.sql` 파일입니다.  
MariaDB 컨테이너 시작 시 `/docker-entrypoint-initdb.d/init.sql`에 마운트되어 **최초 1회만 실행**됩니다
```yaml
configMapGenerator:
- name: sql-init-script
  files:
  - init.sql=init.sql  # 상대경로로 init.sql 지정
```
> ✅ SQL 내용을 바꾸려면 base/init.sql을 수정하거나, 다른 파일 경로를 지정하세요.
---
## 📝 주의사항
- StatefulSet 기반으로 구성되어 있어, Pod가 삭제되더라도 **데이터는 유지됩니다.**
- 데이터를 지우거나, 초기화 하기 위해서는 Database Server 내부에서 진행해야 합니다.
- 또는 PVC를 삭제하고, 새로 생성하는 방법도 있습니다.
    ```sh
    kubectl delete pvc mariadb-data-mariadb-0 -n <goormthon-n>
    ``` 
    ❗ 이 명령은 데이터를 완전히 삭제합니다. 신중히 실행하세요.
---
## 📦 배포 방법
```
kubectl apply -k database/mariadb/overlays
```
---
## 📌 기타 참고
- base는 overlay에서 상속되어 재사용됩니다. env, secret, configMap 등은 overlay에서 수정 가능합니다.
- `overlays/kustomization.yaml`에서 필요한 설정을 추가하거나 수정하여 사용하세요.
- `base/kustomization.yaml`에서 기본 설정을 확인할 수 있습니다.  
변경 사항을 미리 확인하려면 아래 명령어를 사용하세요:

```sh
kustomize build database/mariadb/overlays
```
