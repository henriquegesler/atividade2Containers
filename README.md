# Atividade 2 - Kubernetes (k3d)

## Reimplementação da infraestrutura da aplicação Guess Game

Este projeto consiste na reimplementação da infraestrutura da aplicação **Guess Game** utilizando **Kubernetes (k3d)**, conforme proposto na Atividade 2 da disciplina de Containerização.

A aplicação original não foi modificada, sendo alterada apenas sua infraestrutura de implantação.

---

# Arquitetura

A solução é composta pelos seguintes componentes Kubernetes:

- **Frontend**
  - Deployment
  - Service (NodePort)

- **Backend**
  - Deployment
  - Service (ClusterIP)
  - Horizontal Pod Autoscaler (HPA)

- **Banco de Dados**
  - PostgreSQL
  - Deployment
  - Service (ClusterIP)
  - PersistentVolumeClaim (PVC)

Todos os manifests encontram-se no diretório:

```
k8s/
```

---

# Componentes Kubernetes utilizados

Durante a implementação foram utilizados os seguintes recursos do Kubernetes:

- Deployment
- Service (ClusterIP)
- Service (NodePort)
- PersistentVolumeClaim (PVC)
- Horizontal Pod Autoscaler (HPA)

---

# Imagens Docker

As imagens utilizadas pela aplicação estão publicadas no Docker Hub do aluno.

Backend:

```
hgesler/guess-game-backend:latest
```

Frontend:

```
hgesler/guess-game-frontend:latest
```

Dessa forma, não é necessário reconstruir as imagens para executar a aplicação.

---

# Pré-requisitos

- Docker Desktop
- kubectl
- k3d

---

# Criação do cluster

Criar o cluster k3d:

```bash
k3d cluster create guess-game -p "30080:30080@loadbalancer"
```

Verificar se o cluster foi criado corretamente:

```bash
kubectl get nodes
```

---

# Implantação da aplicação

Na raiz do projeto execute:

```bash
kubectl apply -f k8s/
```

---

# Verificação da implantação

Verificar os Pods:

```bash
kubectl get pods
```

Verificar os Services:

```bash
kubectl get svc
```

Verificar o Horizontal Pod Autoscaler:

```bash
kubectl get hpa
```

---

# Acesso à aplicação

Após todos os Pods estarem no estado **Running**, a aplicação estará disponível em:

```
http://localhost:30080
```

---

# Estrutura da solução

| Componente | Recursos Kubernetes |
|------------|---------------------|
| Frontend | Deployment + Service (NodePort) |
| Backend | Deployment + Service (ClusterIP) |
| PostgreSQL | Deployment + Service (ClusterIP) + PersistentVolumeClaim |
| Escalabilidade | Horizontal Pod Autoscaler (HPA) |

---

# Decisões de implementação

Durante a implementação foram adotadas as seguintes decisões:

- A aplicação original foi mantida sem alterações.
- Toda a infraestrutura foi reimplementada utilizando Kubernetes.
- O frontend é disponibilizado através de um Service do tipo NodePort.
- O backend é executado utilizando múltiplas réplicas em um Deployment.
- O banco PostgreSQL utiliza armazenamento persistente através de PersistentVolumeClaim.
- A comunicação entre os componentes ocorre através de Services internos do Kubernetes.
- O Horizontal Pod Autoscaler monitora a utilização de CPU do backend e ajusta automaticamente a quantidade de réplicas.
- As imagens utilizadas pela aplicação são obtidas diretamente do Docker Hub, não sendo necessária sua reconstrução durante a implantação.
- A configuração do NGINX foi adaptada para o ambiente Kubernetes, direcionando as requisições para o Service do backend, responsável pelo balanceamento entre as réplicas da aplicação.

---

# Organização do projeto

```
k8s/
├── backend.yaml
├── frontend.yaml
├── hpa.yaml
└── postgres.yaml

frontend/
guess/
repository/
tests/

Dockerfile-back
Dockerfile-front
README.md
```