# 9oormthon Kubernetes Infrastructure

> Kubernetes Infrastructure Repository for the 9oormthon Project  
> Database, K8s Manifests 

---

## 📦 Repository Structure

```
.
├── database
│   ├── mariadb
│   │   ├── base
│   │   │   ├── init.sql
│   │   │   ├── kustomization.yaml
│   │   │   ├── secret.yaml
│   │   │   ├── service.yaml
│   │   │   └── statefulset.yaml
│   │   ├── overlays
│   │   │   └── kustomization.yaml
│   │   └── README.md
│   ├── mongodb
│   │   ├── base
│   │   │   ├── create_collction
│   │   │   │   └── job.yaml
│   │   │   ├── kustomization.yaml
│   │   │   ├── secret.yaml
│   │   │   ├── service.yaml
│   │   │   └── statefulset.yaml
│   │   ├── overlays
│   │   │   └── kustomization.yaml
│   │   └── README.md
│   ├── mysql
│   │   ├── base
│   │   │   ├── init.sql
│   │   │   ├── kustomization.yaml
│   │   │   ├── secret.yaml
│   │   │   ├── service.yaml
│   │   │   └── statefulset.yaml
│   │   ├── overlays
│   │   │   └── kustomization.yaml
│   │   └── README.md
│   └── redis
│       ├── base
│       │   ├── kustomization.yaml
│       │   ├── service.yaml
│       │   └── statefulset.yaml
│       └── README.md
├── demo
│   ├── deployment.yaml
│   ├── ingress.yaml
│   ├── kustomization.yaml
│   └── service.yaml
├── k8s
│   ├── backend
│   │   ├── backend.yaml
│   │   ├── config
│   │   │   └── backend-config.json
│   │   ├── ingress.yaml
│   │   ├── kustomization.yaml
│   │   └── README.md
│   └── frontend
│       ├── config
│       │   └── frontend-config.json
│       ├── frontend.yaml
│       ├── ingress.yaml
│       ├── kustomization.yaml
│       └── README.md
└── README.md
```


---

## 🚀 Features

- **모듈화된 앱 구조**: Backend, Frontend, DB를 디렉토리로 분리
- **Kubernetes manifests**: Kustomize 기반의 배포 설정
- **Namespace 격리**: 팀/서비스 단위 네임스페이스 전략

---

## 🛠 Prerequisites

- Kubernetes 클러스터 (EKS)
- `kubectl` 설치
- Docker 및 ECR 접근 권한

---

## ⚙️ Deployment
K8s backend, frontend 폴더에 작성된 README.md 파일을 참고하여 배포하세요.
