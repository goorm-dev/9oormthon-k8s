# 9oormthon Kubernetes Infrastructure

> Kubernetes Infrastructure Repository for the 9oormthon Project  
> 관리형 클러스터, CI/CD, 모니터링, 네임스페이스 분리 등을 포함한 Kubernetes 기반 인프라 구축

---

## 📦 Repository Structure

```
.
├── database/ # Database 배포 YAML 파일
│ ├── mariadb/
│ │ ├── base/
│ │ ├── overlay/
│ │ │ ├── kustomization.yaml # ArgoCD가 바라봐야하는 파일
│ ├── mongodb/
│ │ ├── base/
│ │ ├── overlay/
│ │ │ ├── kustomization.yaml # ArgoCD가 바라봐야하는 파일
│ ├── mysql/
│ │ ├── base/
│ │ ├── overlay/
│ │ │ ├── kustomization.yaml # ArgoCD가 바라봐야하는 파일
│ ├── redis/
│ │ ├── base/
│ │ ├── overlay/
│ │ │ ├── kustomization.yaml # ArgoCD가 바라봐야하는 파일
├── k8s/ # Kubernetes 배포 YAML 파일
│ ├── backend.yaml
│ ├── frontend.yaml
│ ├── ingress.yaml
│ └── kustomization.yaml
└── README.md
```

### 주의: manifests 디렉토리 내의 모든 yaml 파일들의 ECR Repo 경로내에 Team를 수정해주세요!!!

---

## 🚀 Features

- **모듈화된 앱 구조**: Backend, Frontend, DB를 디렉토리로 분리
- **Kubernetes manifests**: Kustomize 기반의 배포 설정
- **CI/CD 연동**: GitHub Actions를 통한 자동 빌드 및 배포
- **Namespace 격리**: 팀/서비스 단위 네임스페이스 전략
- **모니터링 지원**: Prometheus + Grafana 기본 구성 (선택적)
- **보안 고려**: RBAC, NetworkPolicy 등의 정책 설정 가능

---

## 🛠 Prerequisites

- Kubernetes 클러스터 (EKS, GKE, Minikube 등)
- `kubectl` / `kustomize` 설치
- Docker 및 ECR 접근 권한
- GitHub Actions 권한 및 Secrets 설정

---

## ⚙️ Deployment

```bash
cd manifests/
kustomize build . | kubectl apply -f -
