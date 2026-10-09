## 1 - кластер

запустил minikube:

```bash
minikube start
```

## 2 - установка keycloak через helm

добавление и установка репо:

```bash
helm repo add codecentric https://codecentric.github.io/helm-charts

helm install keycloak codecentric/keycloakx \
  --set 'args[0]=start-dev' \
```
