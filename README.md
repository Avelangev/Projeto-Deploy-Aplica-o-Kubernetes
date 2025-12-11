# Projeto-Deploy-Aplica-o-Kubernetes

# Disciplina de Virtualização
## Projeto Fullstack Kubernetes: Flask + React + PostgreSQL

**Grupo: Carolaine Cunha e Avelange Viana**

---

### Sistema de Mensagens - Deploy Kubernetes

Este projeto consiste em uma aplicação fullstack que utiliza a arquitetura de microservices, com deploy completo e gerenciado em Kubernetes, garantindo persistência de dados e alta disponibilidade.

#### Componentes Principais:
* **Frontend:** React + Vite (servido por NGINX)
* **Backend:** Flask + PostgreSQL
* **Banco de Dados:** PostgreSQL com volume persistente (PVC)

#### Características do Deploy em Kubernetes:
* **Exposição:** Ingress NGINX para roteamento de tráfego (domínio único).
* **Configuração:** Uso de ConfigMap e Secrets para variáveis de ambiente.
* **Banco de Dados:** StatefulSet para garantir a ordem e identidade dos Pods, juntamente com Persistent Volume Claim (PVC) para persistência.
* **Alta Disponibilidade:** 2+ réplicas para as aplicações frontend e backend (Deployments).
* **Organização:** Uso de Namespaces dedicados.

### Estrutura do Projeto

 projeto-k8s-deploy/
├── backend/                      # Flask API
│   ├── configmap.yaml            # Variáveis de ambiente do backend
│   └── deployment.yaml           # Deployment do Flask
│
├── database/                     # Banco de Dados PostgreSQL
│   ├── secret.yaml               # Secrets (POSTGRES_USER, DB_PASSWORD, etc.)
│   ├── service.yaml              # Service do PostgreSQL
│   └── statefulset.yaml          # StatefulSet + PVC
│
├── frontend/                     # Aplicação React
│   └── deployment.yaml           # Deployment do Frontend React
│
├── ingress/                      # Ingress Controller
│   └── ingress.yaml              # Regras de rota para "/" e "/api"
│
└── namespace.yaml                # Definição dos namespaces usados

### Pré-requisitos

* cluster Kubernetes
* `kubectl` configurado e autenticado no cluster
* **Ingress NGINX habilitado** (Se estiver usando Minikube: `minikube addons enable ingress`)

### Etapas de Deploy

**Atenção:** Os comandos devem ser executados a partir do diretório raiz (`projeto-k8s-deploy/`).

#### 1. Criar Namespaces
Cria os namespaces `namespace` (para o frontend/backend) e `db-namespace` (para o banco de dados).

```bash
2.Criar Secrets e ConfigMaps Iniciais
# Database Secrets (Credenciais)
kubectl apply -f database/secret.yaml
# Aplica o ConfigMap inicial, **que será corrigido posteriormente**
kubectl apply -f backend/configmap.yaml

3.Subir o PostgreSQL (StatefulSet + PVC)
kubectl apply -f database/statefulset.yaml
kubectl apply -f database/service.yaml

4.Backend - Flask API
kubectl apply -f backend/deployment.yaml

5. Frontend - React + NGINX

6. Ingress Controller
kubectl apply -f ingress/ingress.yaml

7.Acesso à Aplicação
sudo echo "127.0.0.1 app.local" >> /etc/hosts

8. Direcionar api do frontend para o backend 
kubectl port-forward svc/frontend 8080:80 -n namespace
