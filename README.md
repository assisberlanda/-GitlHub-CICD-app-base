# ☸️ Kubernetes
### Deploy de Aplicação com Docker e Kubernetes - GCP
### 1. Ambiente Local
**Pré-requisitos:**
* 🐳 Docker instalado e configurado.
* ☸️ Kubectl instalado e configurado com credenciais do GCP.
* ☁ Google Cloud Platform (GCP) conta com projeto configurado.

**Pasta do projeto:**

```
├── app.js
├── Dockerfile
├── k8s
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── persistent-volume.yaml
└── package.json
```

**Passo a passo:**

**1.1. Criar o código da aplicação Node.js:**

* Crie o arquivo `app.js` com o código da aplicação Node.js.
* Crie o arquivo `package.json` com as dependências da aplicação.
* Adicione um script `start` para iniciar a aplicação no `package.json`:

```json
{
  "name": "node-app",
  "version": "1.0.0",
  "description": "Aplicacao Node.js",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

**1.2. Criar o Dockerfile:**

* Crie o arquivo `Dockerfile` na raiz do projeto.
* Defina a imagem base, os arquivos a serem copiados, as dependências e o comando para iniciar a aplicação:

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "start"]
```

**1.3. Criar a imagem Docker:**

* Execute o comando para criar a imagem Docker:

```bash
docker build -t node-app .
```

**1.4. Testar a imagem Docker localmente:**

* Execute o comando para iniciar um container a partir da imagem criada:

```bash
docker run -p 3000:3000 node-app
```

* Acesse `http://localhost:3000` no navegador para verificar se a aplicação está funcionando.

**1.5. Criar os arquivos YAML do Kubernetes:**

* Crie os arquivos `deployment.yaml`, `service.yaml` e `ingress.yaml` na pasta `k8s`.
* Defina os recursos para deployment, service e ingress:

**`deployment.yaml`:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: node-app:latest
        ports:
        - containerPort: 3000
```

**`service.yaml`:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  selector:
    app: node-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: LoadBalancer
```

**`ingress.yaml`:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: node-app-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: node-app-service
            port:
              number: 80
```

**1.6. Configurar o Persistent Volume (opcional):**

* Crie o arquivo `persistent-volume.yaml` para definir um volume persistente para armazenar dados da aplicação:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: node-app-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/node-app
```

**1.7. Configurar o Persistent Volume Claim (opcional):**

* Crie o arquivo `persistent-volume-claim.yaml` para solicitar um volume persistente:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: node-app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

* **Observação:** A configuração do volume persistente e claim é opcional. Pode ser necessária caso sua aplicação precise armazenar dados de forma persistente.


### 2. Google Cloud Platform (GCP)

**2.1. Criar um cluster Kubernetes:**

* Acesse o Google Kubernetes Engine (GKE) no console do GCP.
* Crie um novo cluster com as configurações desejadas.
* Anote os dados de acesso ao cluster, como endereço IP e credenciais.

**2.2. Aplicar os arquivos YAML do Kubernetes:**

* Use o comando `kubectl` para aplicar os arquivos YAML do Kubernetes no cluster:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

**2.3. Acessar a aplicação:**

* Acesse o endereço IP do load balancer definido no arquivo `ingress.yaml` no navegador.

**2.4. Gerenciar o cluster:**

* Use o comando `kubectl` para gerenciar o cluster, como escalar o número de réplicas, atualizar a imagem Docker, visualizar logs, etc.
* Use o Google Kubernetes Engine (GKE) no console do GCP para gerenciar o cluster.

**2.5. Implementar Continuous Deployment (CD) com CI/CD pipeline:**

* Use uma ferramenta de CI/CD como Jenkins, GitLab CI/CD ou CircleCI para automatizar o processo de build, teste e deploy da aplicação.
* A ferramenta de CI/CD deve ser configurada para monitorar o repositório de código, construir a imagem Docker, aplicar os arquivos YAML no cluster Kubernetes e configurar a versão da imagem no `deployment.yaml`.

### 3. Exemplos de código:

* **`Dockerfile`:**

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "start"]
```

* **`deployment.yaml`:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: node-app:latest
        ports:
        - containerPort: 3000
```

* **`service.yaml`:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  selector:
    app: node-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: LoadBalancer
```

* **`ingress.yaml`:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: node-app-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: node-app-service
            port:
              number: 80
```

**Observação:** O código acima é apenas um exemplo básico. A implementação específica irá depender das necessidades da aplicação e da arquitetura do projeto.
