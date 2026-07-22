# Guess Game - Helm Chart

Este diretório contém o Helm Chart da aplicação Guess Game.

## Estrutura

```
Chart.yaml
values.yaml
templates/
```

O arquivo `values.yaml` centraliza os principais parâmetros da implantação, como:

- imagens Docker;
- número de réplicas;
- recursos dos containers;
- configurações do PostgreSQL;
- PersistentVolumeClaim;
- Horizontal Pod Autoscaler;
- porta NodePort.

## Características da implementação

O Helm Chart mantém a mesma arquitetura utilizada pelos manifests Kubernetes da atividade, adicionando recursos para facilitar a implantação e manutenção da aplicação.

Entre as principais características estão:

- parametrização das configurações da infraestrutura por meio do arquivo `values.yaml`;
- utilização de Liveness Probe e Readiness Probe no backend para monitoramento da disponibilidade da aplicação;
- possibilidade de personalizar imagens, número de réplicas, recursos computacionais, configurações do PostgreSQL e porta de acesso sem modificar os templates.

## Instalação

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