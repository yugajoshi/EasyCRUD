# 1. Deploy Student Registration App using Kubernetes Cluster

First, build images for backend and frontend using Docker.  
Launch EC2 and setup Docker:  
For Docker setup refer: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)  
**OR** if Kubernetes cluster is launched, use CloudShell to build images.

---

### **Backend image:**

- Clone Git repo: [https://github.com/yugajoshi/EasyCRUD.git](https://github.com/yugajoshi/EasyCRUD.git)  
- Use **main** branch.

```bash
cd EasyCRUD/backend/src/main/resources
vim application-properties
```

Paste below content:

```
server.port=8080
spring.datasource.url=jdbc:mariadb://${DB_HOST}:${DB_PORT}/${DB_NAME}
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASS}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

```bash
cd EasyCRUD/backend
docker build . -t dockerhub-username/dockerhub-repo-name:tag
```

---

### **Frontend image:**

```bash
cd EasyCRUD/frontend
```

- Delete `.env` file.  

```bash
cd EasyCRUD/frontend/src/api
vim userService.js
```

Replace:

```js
const BASE_URL = import.meta.env.VITE_API_URL;
```

With:

```js
const BASE_URL = window.APP_CONFIG?.API_URL || "http://localhost:8080";
```

```bash
cd EasyCRUD/frontend
docker build . -t dockerhub-username/dockerhub-repo-name:tag
```

---

### **Check backend and frontend images:**

```bash
docker images
```

---

### **Run frontend (some configuration needs to be done inside the container):**

```bash
docker run -d -p 80:80 <image-name>
docker exec -it <container-ID> sh
cd /var/www/localhost/htdocs/
vim index.html
```

Add below line **before** `</head>`:

```html
<script> config.js </script>
```

Save and exit container.

Build frontend image from modified container:

```bash
docker commit <container-ID> dockerhub-username/dockerhub-repo-name:tag
```

Push backend and frontend images to DockerHub:

```bash
docker login -u username
# Enter DockerHub password or your DockerHub PAT
```

---

## -----------------------------------------------------------------------------------------------------------------------------------

## **First Deployment Type:**  
**switch to devlop branch in EasyCRUD**
**Reference:** [student_app.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/student_app.yaml)

Using simple manifest file and exposing application via **NodePort**

- In this file, configure your database credentials and in ConfigMap, mention the **public IP** of your worker node.

```bash
kubectl apply -f student_app.yaml
```

This will create:
- Service objects
- Deployment objects
- ConfigMap
- Pods

> **Check every object.**  
> Once all pods are running, in browser mention:  
> `http://<worker-node-IP>:30081`

---

## -----------------------------------------------------------------------------------------------------------------------------------

## **Second Deployment Type:**  
**Reference:** [load_balancer_app.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/load_balancer_app.yaml)

Same as NodePort deployment, but uses **LoadBalancer** service object for frontend and backend services.

```bash
kubectl apply -f load_balancer.yaml
kubectl get svc
```

- Copy LoadBalancer DNS/IP of **backend** service.
- Edit `load_balancer.yaml`.

In the **ConfigMap** section, instead of IP, paste backend LoadBalancer **DNS**.

- Apply manifest file again **OR** reconfigure ConfigMap and delete backend pod.  
- Deployment will launch backend pod with latest config.

---

## -----------------------------------------------------------------------------------------------------------------------------------

## **Third Deployment Type:**  
**Reference:**  
- [student_app_with_secret.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/student_app_with_secret.yaml)  
- [secret.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/secret.yaml)

> Only **Secret** object is used in this.  
> This can be implemented in above deployments as well.  

- Refer above file for secret configuration.
- Change values in `secret.yaml`.

Apply in order:

```bash
kubectl apply -f secret.yaml
kubectl apply -f student_app_with_secret.yaml
```

---

## -----------------------------------------------------------------------------------------------------------------------------------

## **Fourth Deployment Type:**  
**Reference:**  
- [student_app_rds.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/student_app_rds.yaml)  
- [secret.yaml](https://github.com/yugajoshi/EasyCRUD/blob/devlop/secret.yaml)

### Steps:

1. First launch **RDS** in AWS. Select **MariaDB**. Mention initial DB name as `student_db`.
2. Launch **temporary EC2** and connect to it:

```bash
sudo -i
apt update -y
apt install mysql-client -y
mysql -h <RDS-DB-Endpoint> -u <username-mentioned-in-rds> -p
```

Enter password mentioned on AWS RDS Console.

```sql
CREATE USER '<rds-username>'@'*' IDENTIFIED BY '<password>';
GRANT ALL PRIVILEGES ON `student_db`.* TO '<rds-username>'@'*' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

3. Configure `secret.yaml` with latest configurations.

4. Apply manifests:

```bash
kubectl apply -f secret.yaml
kubectl apply -f student_app_rds.yaml
```

---
