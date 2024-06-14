# Instalação do Keycloak Usando Helm no Kubernetes

Este guia fornece instruções para instalar o Keycloak usando Helm em um cluster Kubernetes.

## Pré-requisitos

- Helm instalado. Você pode encontrar as instruções de instalação [aqui](https://helm.sh/docs/intro/install/).
- Um cluster Kubernetes funcional.

## Passos para Instalar o Keycloak

### 1. Instalar o Helm

Se você ainda não instalou o Helm, siga as instruções [aqui](https://helm.sh/docs/intro/install/).

### 2. Adicionar o Repositório Helm da Bitnami

Adicione o repositório da Bitnami ao seu Helm e atualize-o.

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 3. Criar um Namespace

Crie um namespace dedicado para o Keycloak.

```sh
kubectl create namespace keycloak
```

### 4.  Instalar o Keycloak

Use o chart Helm para instalar o Keycloak. Para uma instalação básica, execute:

```sh
helm install keycloak bitnami/keycloak --namespace keycloak
```

Se você tiver um arquivo `values.yaml` para configurações personalizadas, use:

```sh
helm install keycloak bitnami/keycloak --namespace keycloak -f values.yaml
```

### 5. Verificar a Instalação

Verifique o status da instalação para garantir que o Keycloak foi instalado corretamente.

```sh
helm status keycloak --namespace keycloak
```

### 6. Acessar o Keycloak

Por padrão, o chart Helm da Bitnami expõe o Keycloak usando um serviço Kubernetes do tipo LoadBalancer. Obtenha o IP ou nome DNS do LoadBalancer:

```sh
kubectl get svc --namespace keycloak
```

Procure pelo serviço chamado `keycloak`. Alternativamente, você pode usar port-forwarding para acessar o Keycloak localmente:

```sh
kubectl port-forward svc/keycloak 8080:8080 --namespace keycloak
```

Acesse o Keycloak em `http://localhost:8080`.

### 7. Obter Credenciais de Administrador

As credenciais de administrador padrão são armazenadas em um segredo do Kubernetes. Recupere-as usando:

```sh
kubectl get secret --namespace keycloak keycloak -o jsonpath="{.data.admin-password}" | base64 --decode
kubectl get secret --namespace keycloak keycloak -o jsonpath="{.data.admin-user}" | base64 --decode
```

Use essas credenciais para fazer login no console de administração do Keycloak.

### Exemplo de Arquivo `values.yaml`

Aqui está um exemplo de como pode ser um arquivo `values.yaml` para personalizar a sua implantação do Keycloak:

```yaml
replicaCount: 1

service:
  type: LoadBalancer

postgresql:
  enabled: true
  postgresqlUsername: keycloak
  postgresqlPassword: keycloakpassword
  postgresqlDatabase: keycloak

keycloak:
  auth:
    adminUser: admin
    adminPassword: adminpassword
```

> Você pode incluir configurações adicionais, como solicitações e limites de recursos, configurações de persistência, e assim por diante.

Notas
---

- Certifique-se de que seu cluster Kubernetes tem recursos suficientes para rodar o Keycloak.
- Configure DNS ou use Ingress para um melhor gerenciamento de acesso em um ambiente de produção.
- Considere proteger o Keycloak com SSL/TLS, especialmente em um ambiente de produção. Você pode precisar configurar o Ingress com TLS ou usar uma configuração de proxy reverso.

Seguindo esses passos, você deve ter uma instância funcional do Keycloak rodando no seu cluster Kubernetes.
