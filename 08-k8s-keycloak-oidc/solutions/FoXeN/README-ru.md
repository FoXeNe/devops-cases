## 1 - кластер

запуск minikube:

```bash
minikube start
```

## 2 - установка keycloak через helm

добавление и установка репо:

```bash
helm repo add codecentric https://codecentric.github.io/helm-charts

helm install keycloak codecentric/keycloakx --set 'args[0]=start-dev'
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
после регистрации пользователя создаем новый realm, дальше переходим во вкладку identity providers -> ваш провайдер(в моем случае гитхаб) и копируете redurect uri. регистрируете OAuth App, вставляете redirect rui в гитхаб в settings -> Developer settings -> OAuth apps -> выбираете app -> `Redirect URI` и регистрируете OAuth app. в поле `homepage url` вставляем `http://localhost:8080`

## 5 - kube-apiserver

так как api server может обращаться только к https, то нам надо сделать ingress контроллер внутри пода и сделать сертефикат

включаем ingress:
```bash
minikube addons enable ingress
```
узнаем ip и посмотрим создался ли сервис nginx:
```bash
minikube ip
kubectl get svc -n ingress-nginx
NAME                                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.103.133.162   <none>        80:31438/TCP,443:30170/TCP   6m34s
ingress-nginx-controller-admission   ClusterIP   10.110.69.122    <none>        443/TCP                      6m34s
```
проверим поды:
```bash
kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-svf2c        0/1     Completed   0          4m26s
ingress-nginx-admission-patch-rwf8n         0/1     Completed   1          4m26s
ingress-nginx-controller-596f8778bc-982wn   1/1     Running     0          4m26s
```
все работает, теперь нужно составить адрес по которому мы можем получить доступ к ingress, это `https://keycloak.<minikube ip>.nip.io:port` и проверим работает ли nginx:
```bash
curl -k <адрес-который-сделали-выше>
```

