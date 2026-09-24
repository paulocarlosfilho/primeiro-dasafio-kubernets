# 🚀 Primeiro Desafio Kubernetes

Projeto focado na orquestração de microsserviços utilizando **Kubernetes (Kind)**, **Docker** e **Metrics Server** para auto-scaling das aplicações.

---

## 🛠️ Arquitetura e Tecnologias

- **Aplicações:** 3 APIs Node.js (`Desafio 1`, `Desafio 2`, `Desafio 3`)
- **Banco de Dados & Cache:** PostgreSQL e Redis
- **Orquestração:** Kubernetes (Kind)
- **Auto-Scaling:** Horizontal Pod Autoscaler (HPA) com Metrics Server
- **Container Registry:** Docker Hub

---

## 📁 Estrutura do Projeto

```text
.
├── Desafio 1/      # Código-fonte e Dockerfile da API 1 (Porta 3000)
├── Desafio 2/      # Código-fonte e Dockerfile da API 2 (Porta 3001)
├── Desafio 3/      # Código-fonte e Dockerfile da API 3 (Porta 3002)
└── k8s/            # Manifestos YAML do Kubernetes
    ├── configmap.yaml
    ├── secrets.yaml
    ├── postgres-pv.yaml
    ├── postgres.yaml
    ├── redis-pv.yaml
    ├── redis.yaml
    ├── desafio1.yaml
    ├── desafio2.yaml
    ├── desafio3.yaml
    └── hpa.yaml
```

## ⚙️ Como Executar o Projeto

### 1. Pré-requisitos

* Docker Desktop rodando com cluster Kind ativo

* kubectl instalado e configurado

### 2. Build e Push das Imagens Docker

Gere as imagens locais e envie para o Docker Hub:

```code
# Build das imagens
docker build -t paulocarlosfilho/desafio1-api:v1.0.1 ./"Desafio 1"
docker build -t paulocarlosfilho/desafio2-api:v1.0.1 ./"Desafio 2"
docker build -t paulocarlosfilho/desafio3-api:v1.0.1 ./"Desafio 3"

# Autenticação e Envio
docker login
docker push paulocarlosfilho/desafio1-api:v1.0.1
docker push paulocarlosfilho/desafio2-api:v1.0.1
docker push paulocarlosfilho/desafio3-api:v1.0.1
```

### 3. Deploy no Kubernetes

Com o cluster Kind em execução, aplique todos os manifestos contidos na pasta k8s:

```code
kubectl apply -f k8s/
```

### 4. Configurar Metrics Server (Para funcionamento dos HPAs)

Para habilitar a leitura de métricas de CPU no Kind:

```code
# Instalar o Metrics Server no cluster
kubectl apply -f [https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml](https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml)

# Liberar a verificação TLS (exigido pelo Kind)
kubectl patch deployment metrics-server -n kube-system --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'
```

### 🔍 Comandos para Verificação

Verificar o status de todos os recursos (Pods, Services, Deployments):

```code
kubectl get all
```

Verificar o funcionamento das métricas de Auto-scaling (HPA):

```code
kubectl get hpa
```

Verificar os Volumes Persistentes (PV e PVC):

```code
kubectl get pv,pvc
```