# Пошаговое ручное развертывание Kubernetes

## Подготовка

### 1. Запустить Minikube

```
minikube start --driver=docker
```

### 2. Включить Ingress
```
minikube addons enable ingress
```

## Этап 1: Создание Namespace и конфигурации

### Шаг 1.1 - Создать namespace
```
kubectl apply -f namespace.yaml
```

### Шаг 1.2 - Создать Secrets
```
kubectl apply -f secrets.yaml
```

### Шаг 1.3 - Создать ConfigMap
```
kubectl apply -f configmap.yaml
```

## Этап 2: Развертывание баз данных (PostgreSQL, MongoDB, Redis)

```
kubectl apply -f databases/
```

## Этап 3: Развертывание Kafka

### Шаг 3.1 - Zookeeper

```
kubectl apply -f kafka/zookeeper-deployment.yaml
kubectl apply -f kafka/zookeeper-service.yaml
```

### Шаг 3.2 - Kafka

```
kubectl apply -f kafka/kafka-deployment.yaml
kubectl apply -f kafka/kafka-service.yaml
```

Проверка и ожидание:
```
kubectl get pods -n marketplace -l app=kafka
kubectl wait --for=condition=ready pod -l app=kafka -n marketplace --timeout=300s
```

## Этап 4: Развертывание микросервисов

### Шаг 4.1 - Auth Service

```
kubectl apply -f auth-service/deployment.yaml
kubectl apply -f auth-service/service.yaml
```

Просмотр логов:
```
kubectl logs -n marketplace -f deployment/auth-service
```

### Шаг 4.2 - User Service

```
kubectl apply -f user-service/deployment.yaml
kubectl apply -f user-service/service.yaml
```

Просмотр логов:
```
kubectl logs -n marketplace -f deployment/user-service
```

### Шаг 4.3 - Order Service

```
kubectl apply -f order-service/deployment.yaml
kubectl apply -f order-service/service.yaml
```

Просмотр логов:
```
kubectl logs -n marketplace -f deployment/order-service
```

### Шаг 4.4 - Payment Service

```
kubectl apply -f payment-service/deployment.yaml
kubectl apply -f payment-service/service.yaml
```

Просмотр логов:
```
kubectl logs -n marketplace -f deployment/payment-service
```

### Шаг 4.5 - API Gateway

```
kubectl apply -f api-gateway/deployment.yaml
kubectl apply -f api-gateway/service.yaml
```

Просмотр логов:
```
kubectl logs -n marketplace -f deployment/api-gateway
```

## Этап 5: Развертывание Ingress

```
kubectl apply -f ingress.yaml
```

Проверка:
```
kubectl get ingress -n marketplace
```

## Проверка полного развертывания

### Просмотр всех pods
```
kubectl get pods -n marketplace
```

Все pods должны быть в статусе `Running` и `1/1` Ready.

## Доступ к сервисам

### Port-Forward

#### API Gateway
```
kubectl port-forward -n marketplace svc/api-gateway 8079:8079
```

Доступ: `http://localhost:8079`