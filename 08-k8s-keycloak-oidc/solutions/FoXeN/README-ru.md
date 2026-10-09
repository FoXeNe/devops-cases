## 1 - кластер

запуск minikube:

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

для доступа к ui нам нужен сервис http. пробросим его порты на хост:

```bash
kubectl port-forward svc/keycloak-keycloakx-http 8080:80
```

теперь можем зайти на сайт по адресу <http://localhost:8080> и зарегистрировать нового пользователя

## 4 - подключение к github

# TODO подробнее описать регистрацию организации в гитхабе

для начала вам нужно создать организацию в github

# TODO более внятно все это расписать
после регистрации пользователя создаем новый realm, дальше переходим во вкладку identity providers -> ваш провайдер(в моем случае гитхаб) и копируете redurect uri. вставляете его в OAuth app в github в поле `redirect uri` и регистрируете OAuth app. в поле `homepage url` вставляем `http://localhost:8080`
