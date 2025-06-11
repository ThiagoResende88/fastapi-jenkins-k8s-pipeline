# Projeto DevOps: Pipeline de CI/CD para API FastAPI com Jenkins e Kubernetes

Este projeto demonstra a criação de uma pipeline de integração e entrega contínua (CI/CD) para uma aplicação backend desenvolvida com o framework FastAPI. O processo é totalmente automatizado usando Jenkins e o deploy é realizado em um cluster Kubernetes local (Minikube).

## 🚀 Tecnologias Utilizadas

* **Backend:** FastAPI (Python)
* **Containerização:** Docker & Docker Hub
* **Automação (CI/CD):** Jenkins
* **Orquestração:** Kubernetes (Minikube)

## 📂 Estrutura do Projeto

```
.
├── backend/
│   ├── main.py
│   ├── requirements.txt
│   └── Dockerfile
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── Jenkinsfile
```

## ⚙️ Fases do Projeto e Execução

O projeto foi dividido em fases para uma construção incremental da solução.

### 1. Aplicação FastAPI

O diretório `backend/` contém uma simples API com múltiplos endpoints, pronta para ser containerizada.

### 2. Containerização com Docker

O `backend/Dockerfile` contém as instruções para criar uma imagem Docker da aplicação. A imagem é enviada para o Docker Hub para que o Kubernetes possa acessá-la.

### 3. Deploy Manual no Kubernetes

Os manifestos em `k8s/` (`deployment.yaml` e `service.yaml`) descrevem como a aplicação deve ser executada e exposta no Kubernetes.

Para aplicar manualmente:
```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```
Para acessar a aplicação no Minikube:
```bash
minikube service fastapi-service
```

### 4. Pipeline Automatizada com Jenkins

O `Jenkinsfile` na raiz do projeto orquestra todo o processo de CI/CD.

**Etapas da Pipeline:**
1.  **Build Docker Image:** Constrói a imagem Docker da aplicação a partir do `Dockerfile`.
2.  **Push to Docker Hub:** Autentica-se no Docker Hub e envia a nova imagem com uma tag única (o número do build).
3.  **Deploy to Kubernetes:** Conecta-se ao cluster Kubernetes e atualiza a aplicação para usar a imagem recém-criada, garantindo um deploy sem interrupções.

### ✅ Resultado Final

Abaixo, uma imagem da pipeline no Jenkins executando todas as etapas com sucesso.
![image](https://github.com/user-attachments/assets/514876ab-4db4-4cbc-8b0d-42e576946be9)
![image](https://github.com/user-attachments/assets/aaf9ea0e-830a-4abb-acb4-77dddb201da8)


