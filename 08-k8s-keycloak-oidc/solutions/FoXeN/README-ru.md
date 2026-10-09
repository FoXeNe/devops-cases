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

## 3 - проброс портов и настройка keycloak

для начала посмотрим доступные сервисы:

```bash
➜  ~ kubectl get svc                                         
NAME                          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                    AGE
keycloak-keycloakx-headless   ClusterIP   None            <none>        80/TCP                     9m55s
keycloak-keycloakx-http       ClusterIP   10.104.60.149   <none>        9000/TCP,80/TCP,8443/TCP   9m55s
kubernetes                    ClusterIP   10.96.0.1       <none>        443/TCP                    29h
```

для доступа к ui нам нужен серрвис http. пробросим его порты на хост:

```bash
kubectl port-forward svc/keycloak-keycloakx-http 8080:80
```

теперь можем зайти на сайт по адресу <http://localhost:8080> и зарегистрировать нового пользователя
