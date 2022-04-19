# [djangokubernetes](https://medium.com/better-programming/how-to-deploy-a-django-application-with-kubernetes-f5814b0688bf)

Follow the steps in the above link, to spin-up a locally running version of the app ...

## Local Setup
`docker build -t djangokubernetes .`

Make sure the image is built

```
❯ docker images
REPOSITORY                    TAG       IMAGE ID       CREATED          SIZE
djangokubernetes              latest    f9f2f2e2c1f7   27 minutes ago   526MB
```

Make migrations and create an admin account

```
❯ docker run -i -t djangokubernetes sh
# python3 manage.py makemigrations && python3 manage.py migrate
No changes detected
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
# python3 manage.py createsuperuser
Username (leave blank to use 'root'): admin
Email address:
Password:
Password (again):
```

Check and make sure the app can be run ...

`docker run -p 80:8000 djangokubernetesproject`

Visit the app landing page at `http://localhost/`

## Dockerhub
Inorder for kubernetes to deply the app it needs to be available on dockerhub
```
docker login

docker tag djangokubernetes:latest your_dockerhub_username/djangokubernetes:latest

docker push your_dockerhub_username/djangokubernetes:latest
```
## Kubernetes (minikube)
Once the kubernetes files are created, apply the deployment file
`kubectl apply -f django-deployment.yaml` and the nodeport service `kubectl apply -f django-svc.yaml`

Make sure pods are running 
```
❯ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
django-app-f97d7b967-dgjlj   1/1     Running   0          27m
django-app-f97d7b967-kllw5   1/1     Running   0          28m
django-app-f97d7b967-tw8dv   1/1     Running   0          27m
```
Make sure service is running
```
❯ kubectl get svc django
NAME     TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
django   NodePort   10.97.238.144   <none>        8000:32550/TCP   26m
```
 
 
 ### Visit site running locally
 ```
 minikube service django --url
 ```
 will give you the url you can visit where the app is running
