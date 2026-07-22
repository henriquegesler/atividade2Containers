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

As imagens utilizadas pela aplicação estão publicadas no Docker Hub.

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

> **Observação:** O Pod do PostgreSQL pode levar alguns segundos para concluir sua inicialização na primeira execução. Aguarde até que todos os Deployments estejam concluídos e os Pods estejam no estado **Running** antes de acessar a aplicação.

---

# Verificação da implantação

Aguarde a conclusão dos Deployments:

```bash
kubectl rollout status deployment/postgres
kubectl rollout status deployment/backend
kubectl rollout status deployment/frontend
```

Verifique os Pods:

```bash
kubectl get pods
```

Verifique os Services:

```bash
kubectl get svc
```

Verifique o Horizontal Pod Autoscaler:

```bash
kubectl get hpa
```

---

# Acesso à aplicação

Após todos os Deployments terem sido concluídos e os Pods estarem no estado **Running**, a aplicação estará disponível em:

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
- O banco PostgreSQL utiliza armazenamento persistente através de um PersistentVolumeClaim.
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
---

# Implementação Adicional - Helm Charts

Como implementação adicional, foi desenvolvido um **Helm Chart** para a aplicação, permitindo que toda a infraestrutura Kubernetes seja implantada por meio de um único comando.

O Helm foi utilizado como uma camada de abstração sobre os manifests Kubernetes já desenvolvidos, mantendo a mesma arquitetura e comportamento da aplicação.

Essa implementação tem como objetivo facilitar a instalação, centralizar configurações em um único arquivo (`values.yaml`) e demonstrar a utilização de uma ferramenta amplamente empregada na implantação e gerenciamento de aplicações Kubernetes.

A estrutura criada encontra-se no diretório:

```
helm/
└── guess-game/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
```

Os templates Helm foram construídos a partir dos manifests Kubernetes originais, mantendo a mesma arquitetura da aplicação e centralizando as principais configurações no arquivo `values.yaml`. Além da parametrização dos recursos, a implementação adiciona mecanismos nativos do Kubernetes para aumentar a robustez da implantação.

Foram parametrizados os seguintes itens:

- imagens Docker;
- número de réplicas;
- recursos de CPU e memória;
- configurações do PostgreSQL;
- tamanho do PersistentVolumeClaim;
- configuração do Horizontal Pod Autoscaler;
- porta de acesso da aplicação.

Além disso, a implementação Helm incorpora melhorias específicas para o ambiente Kubernetes, incluindo:

- configuração de **Liveness Probe** e **Readiness Probe** no backend utilizando o endpoint `/health`;
- centralização das configurações da implantação no arquivo `values.yaml`, facilitando a reutilização do Chart em diferentes ambientes sem necessidade de alterar os templates.

### Implantação utilizando Helm

Criar um cluster k3d:

```bash
k3d cluster create guess-game -p "30080:30080@loadbalancer"
```

Instalar o Chart:

```bash
helm install guess-game .
```

Verificar os recursos:

```bash
kubectl get pods
kubectl get svc
kubectl get hpa
```

A aplicação estará disponível em:

```
http://localhost:30080
```

A utilização do Helm não substitui a implantação apresentada anteriormente por meio dos manifests Kubernetes, sendo disponibilizada como uma implementação adicional da infraestrutura.