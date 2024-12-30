
# Kubernetes for Docker Users

This guide helps Docker users to transition smoothly to Kubernetes by using `kubectl` commands, which are similar to Docker commands. Below are common Docker commands with their equivalent `kubectl` commands.

## Docker `run` vs `kubectl create deployment`

### Docker
```bash
docker run -d --restart=always -e DOMAIN=cluster --name nginx-app -p 80:80 nginx
```

### `kubectl`
```bash
# Start the pod running nginx
kubectl create deployment --image=nginx nginx-app
deployment.apps/nginx-app created

# Add environment variable to nginx-app
kubectl set env deployment/nginx-app DOMAIN=cluster
deployment.apps/nginx-app env updated

# Expose port through a service
kubectl expose deployment nginx-app --port=80 --name=nginx-http
service "nginx-http" exposed
```
**Note:** `kubectl` commands will print the type and name of the resource created or mutated, which can then be used in subsequent commands.

## Docker `ps` vs `kubectl get`

### Docker
```bash
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                     PORTS                NAMES
14636241935f        ubuntu:16.04        "echo test"              5 seconds ago        Exited (0) 5 seconds ago                        cocky_fermi
55c103fa1296        nginx               "nginx -g 'daemon of…"   About a minute ago   Up About a minute          0.0.0.0:80->80/tcp   nginx-app
```

### `kubectl`
```bash
kubectl get po
NAME                        READY     STATUS      RESTARTS   AGE
nginx-app-8df569cb7-4gd89   1/1       Running     0          3m
ubuntu                      0/1       Completed   0          20s
```

## Docker `attach` vs `kubectl attach`

### Docker
```bash
docker attach 55c103fa1296
```

### `kubectl`
```bash
kubectl attach -it nginx-app-5jyvm
```
**Note:** To detach from the container, use the escape sequence `Ctrl+P` followed by `Ctrl+Q`.

## Docker `exec` vs `kubectl exec`

### Docker
```bash
docker exec 55c103fa1296 cat /etc/hostname
nginx-app-5jyvm
```

### `kubectl`
```bash
kubectl exec nginx-app-5jyvm -- cat /etc/hostname
nginx-app-5jyvm

# Use interactive commands
kubectl exec -ti nginx-app-5jyvm -- /bin/sh
# exit
```

## Docker `logs` vs `kubectl logs`

### Docker
```bash
docker logs -f a9e
192.168.9.1 - - [14/Jul/2015:01:04:02 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.35.0" "-"
```

### `kubectl`
```bash
kubectl logs -f nginx-app-zibvs
10.240.63.110 - - [14/Jul/2015:01:09:01 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.26.0" "-"
```

### Previous logs
```bash
kubectl logs --previous nginx-app-zibvs
10.240.63.110 - - [14/Jul/2015:01:09:01 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.26.0" "-"
```

## Docker `stop` and `rm` vs `kubectl delete`

### Docker
```bash
docker stop a9ec34d98787
docker rm a9ec34d98787
```

### `kubectl`
```bash
kubectl get deployment nginx-app
kubectl get po -l app=nginx-app
kubectl delete deployment nginx-app
deployment "nginx-app" deleted
kubectl get po -l app=nginx-app
```
**Note:** When using `kubectl`, you delete the deployment, which deletes the associated pod.

## Docker `login` vs Kubernetes private registry

There is no direct `docker login` analog in `kubectl`. However, if you're using Kubernetes with a private registry, see [Using a Private Registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/) for more details.

## Docker `version` vs `kubectl version`

### Docker
```bash
docker version
Client version: 1.7.0
Client API version: 1.19
Server version: 1.7.0
Server API version: 1.19
```

### `kubectl`
```bash
kubectl version
Client Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.9+a3d1dfa6f4335", GitCommit:"9b77fed11a9843ce3780f70dd251e92901c43072", GitTreeState:"dirty", BuildDate:"2017-08-29T20:32:58Z", OpenPaasKubernetesVersion:"v1.03.02", GoVersion:"go1.7.5", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.9+a3d1dfa6f4335", GitCommit:"9b77fed11a9843ce3780f70dd251e92901c43072", GitTreeState:"dirty", BuildDate:"2017-08-29T20:32:58Z", OpenPaasKubernetesVersion:"v1.03.02", GoVersion:"go1.7.5", Compiler:"gc", Platform:"linux/amd64"}
```

## Docker `info` vs `kubectl cluster-info`

### Docker
```bash
docker info
Containers: 40
Images: 168
Storage Driver: aufs
Kernel Version: 3.13.0-53-generic
Operating System: Ubuntu 14.04.2 LTS
CPUs: 12
Total Memory: 31.32 GiB
```

### `kubectl`
```bash
kubectl cluster-info
Kubernetes master is running at https://203.0.113.141
KubeDNS is running at https://203.0.113.141/api/v1/namespaces/kube-system/services/kube-dns/proxy
```

---

This comparison will help you transition from Docker to Kubernetes while managing your containers and services.

# Comprehensive Kubernetes `kubectl` Commands

## **Cluster Information**
1. Show the Kubernetes version:
   ```bash
   kubectl version
   ```
   - **Example Output:**
     ```
     Client Version: v1.26.0
     Server Version: v1.26.0
     ```
   - **Purpose:** Helps verify the compatibility between your client and server.

2. Display cluster information:
   ```bash
   kubectl cluster-info
   ```
   - **Example Output:**
     ```
     Kubernetes control plane is running at https://127.0.0.1:6443
     CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
     ```

3. List all nodes in the cluster:
   ```bash
   kubectl get nodes
   ```
   - **Example Output:**
     ```
     NAME         STATUS   ROLES    AGE   VERSION
     worker-node1 Ready    <none>   10d   v1.26.0
     ```
   - **Purpose:** To check the nodes participating in the Kubernetes cluster.

4. Describe a specific node:
   ```bash
   kubectl describe node <node-name>
   ```
   - **Purpose:** Displays detailed information about a node.

5. List all namespaces:
   ```bash
   kubectl get namespaces
   ```
   - **Example Output:**
     ```
     NAME              STATUS   AGE
     default           Active   10d
     kube-system       Active   10d
     kube-public       Active   10d
     ```

6. Get the current context:
   ```bash
   kubectl config current-context
   ```
   - **Purpose:** Identifies the active Kubernetes context.

---

## **Namespaces**
1. Create a namespace:
   ```bash
   kubectl create namespace dev
   ```
   - **Purpose:** Segregate resources for the `dev` environment.
   - **Example Output:**
     ```
     namespace/dev created
     ```

2. Delete a namespace:
   ```bash
   kubectl delete namespace dev
   ```
   - **Purpose:** Removes the `dev` environment and all its resources.

3. List all namespaces:
   ```bash
   kubectl get namespaces
   ```
   - **Example Output:**
     ```
     NAME              STATUS   AGE
     default           Active   10d
     dev               Active   1m
     ```

4. Describe a namespace:
   ```bash
   kubectl describe namespace dev
   ```
   - **Purpose:** Provides details about the `dev` namespace.

---

## **Pods**
1. List all pods in the default namespace:
   ```bash
   kubectl get pods
   ```
   - **Example Output:**
     ```
     NAME          READY   STATUS    RESTARTS   AGE
     nginx-pod     1/1     Running   0          5m
     ```

2. List pods in a specific namespace:
   ```bash
   kubectl get pods -n dev
   ```
   - **Purpose:** Checks the status of pods in the `dev` namespace.

3. Get detailed information about a pod:
   ```bash
   kubectl describe pod nginx-pod -n dev
   ```
   - **Purpose:** Shows events and details of the `nginx-pod`.

4. Check logs of a pod:
   ```bash
   kubectl logs nginx-pod -n dev
   ```
   - **Purpose:** Helps debug pod-related issues.

5. Tail live logs of a pod:
   ```bash
   kubectl logs -f nginx-pod -n dev
   ```
   - **Purpose:** Monitor logs in real time.

6. Execute a command inside a pod:
   ```bash
   kubectl exec -it nginx-pod -n dev -- /bin/sh
   ```
   - **Purpose:** Open a shell session inside the `nginx-pod`.

---

## **Deployments**
1. List all deployments:
   ```bash
   kubectl get deployments
   ```
   - **Example Output:**
     ```
     NAME             READY   UP-TO-DATE   AVAILABLE   AGE
     nginx-deploy     1/1     1            1           5m
     ```

2. Create a deployment:
   ```bash
   kubectl create deployment nginx-deploy --image=nginx
   ```
   - **Purpose:** Launch an NGINX web server in the cluster.
   - **Example Output:**
     ```
     deployment.apps/nginx-deploy created
     ```

3. Update the image of a deployment:
   ```bash
   kubectl set image deployment/nginx-deploy nginx-container=nginx:1.19
   ```
   - **Purpose:** Updates the container image to a newer version.

4. Check the rollout status:
   ```bash
   kubectl rollout status deployment/nginx-deploy
   ```
   - **Purpose:** Verify the success of an update.

5. Scale a deployment:
   ```bash
   kubectl scale deployment/nginx-deploy --replicas=3
   ```
   - **Purpose:** Increase the number of pods to handle traffic.
   - **Example Output:**
     ```
     deployment.apps/nginx-deploy scaled
     ```

---

## **Services**
1. Expose a deployment as a service:
   ```bash
   kubectl expose deployment nginx-deploy --type=NodePort --port=80
   ```
   - **Purpose:** Make the NGINX deployment accessible within the cluster or externally.
   - **Example Output:**
     ```
     service/nginx-deploy exposed
     ```

2. List all services:
   ```bash
   kubectl get services
   ```
   - **Example Output:**
     ```
     NAME           TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
     nginx-deploy   NodePort   10.96.120.40   <none>        80:31000/TCP   2m
     ```

3. Describe a service:
   ```bash
   kubectl describe service nginx-deploy
   ```
   - **Purpose:** Provides detailed information about the `nginx-deploy` service.

---

## **ConfigMaps and Secrets**
1. Create a ConfigMap:
   ```bash
   kubectl create configmap app-config --from-literal=ENV=production
   ```
   - **Purpose:** Store environment-specific variables.
   - **Example Output:**
     ```
     configmap/app-config created
     ```

2. Create a Secret:
   ```bash
   kubectl create secret generic db-credentials --from-literal=username=admin --from-literal=password=secret123
   ```
   - **Purpose:** Securely store sensitive information.
   - **Example Output:**
     ```
     secret/db-credentials created
     ```

3. List ConfigMaps:
   ```bash
   kubectl get configmaps
   ```

4. List Secrets:
   ```bash
   kubectl get secrets
   ```

---

## **RBAC (Role-Based Access Control)**
1. Create a ClusterRole:
   ```bash
   kubectl create clusterrole read-pods --verb=get,list,watch --resource=pods
   ```
   - **Purpose:** Define permissions to read pod information.
   - **Example Output:**
     ```
     clusterrole.rbac.authorization.k8s.io/read-pods created
     ```

2. Bind a ClusterRole to a user:
   ```bash
   kubectl create clusterrolebinding read-pods-binding --clusterrole=read-pods --user=<username>
   ```
   - **Purpose:** Assign the `read-pods` ClusterRole to a specific user.
   - **Example Output:**
     ```
     clusterrolebinding.rbac.authorization.k8s.io/read-pods-binding created
     ```

3. Create a Role in a namespace:
   ```bash
   kubectl create role pod-reader --verb=get,list,watch --resource=pods -n dev
   ```
   - **Purpose:** Define permissions for a specific namespace.

4. Bind a Role to a service account:
   ```bash
   kubectl create rolebinding pod-reader-binding --role=pod-reader --serviceaccount=dev:default -n dev
   ```
   - **Purpose:** Assign the `pod-reader` Role to the default service account in the `dev` namespace.

---

## **Persistent Volumes and Persistent Volume Claims**
1. List all persistent volumes:
   ```bash
   kubectl get pv
   ```
2. Describe a persistent volume:
   ```bash
   kubectl describe pv <pv-name>
   ```
3. List persistent volume claims:
   ```bash
   kubectl get pvc
   ```
4. Describe a persistent volume claim:
   ```bash
   kubectl describe pvc <pvc-name>
   ```

### **Examples**
1. Create a Persistent Volume:
   ```yaml
 apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
    type: DirectoryOrCreate
   ```
   - **Command


## **StatefulSets and DaemonSets**

1. **List StatefulSets**:
   ```bash
   kubectl get statefulsets
   ```
   Example:
   ```bash
   NAME            DESIRED   CURRENT   AGE
   my-statefulset  3         3         2d
   ```

2. **Describe a StatefulSet**:
   ```bash
   kubectl describe statefulset <statefulset-name>
   ```
   Example:
   ```bash
   kubectl describe statefulset my-statefulset
   ```

3. **List DaemonSets**:
   ```bash
   kubectl get daemonsets
   ```
   Example:
   ```bash
   NAME            DESIRED   CURRENT   AGE
   my-daemonset    3         3         2d
   ```

4. **Describe a DaemonSet**:
   ```bash
   kubectl describe daemonset <daemonset-name>
   ```
   Example:
   ```bash
   kubectl describe daemonset my-daemonset
   ```

---

## **Jobs and CronJobs**

1. **List Jobs**:
   ```bash
   kubectl get jobs
   ```
   Example:
   ```bash
   NAME            COMPLETIONS   DURATION   AGE
   my-job          1/1           1m         2d
   ```

2. **Describe a Job**:
   ```bash
   kubectl describe job <job-name>
   ```
   Example:
   ```bash
   kubectl describe job my-job
   ```

3. **List CronJobs**:
   ```bash
   kubectl get cronjobs
   ```
   Example:
   ```bash
   NAME            SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
   my-cronjob      * * * * *   False     1        2m              3d
   ```

4. **Describe a CronJob**:
   ```bash
   kubectl describe cronjob <cronjob-name>
   ```
   Example:
   ```bash
   kubectl describe cronjob my-cronjob
   ```

---

## **RBAC**

1. **List roles and role bindings**:
   ```bash
   kubectl get roles,rolebindings
   ```
   Example:
   ```bash
   NAME                  AGE
   role/my-role          3d
   rolebinding/my-bind   3d
   ```

2. **Describe a role**:
   ```bash
   kubectl describe role <role-name>
   ```
   Example:
   ```bash
   kubectl describe role my-role
   ```

3. **Describe a role binding**:
   ```bash
   kubectl describe rolebinding <binding-name>
   ```
   Example:
   ```bash
   kubectl describe rolebinding my-bind
   ```

---

## **Troubleshooting**

1. **View events**:
   ```bash
   kubectl get events --sort-by=.metadata.creationTimestamp
   ```
   Example:
   ```bash
   LAST SEEN   TYPE      REASON      OBJECT         MESSAGE
   10s         Normal    Scheduled   pod/my-pod      Successfully assigned my-pod to node-1
   ```

2. **Check cluster logs**:
   ```bash
   kubectl logs -n kube-system <pod-name>
   ```
   Example:
   ```bash
   kubectl logs -n kube-system kube-dns-xyz-1234
   ```

3. **Run a busybox pod for debugging**:
   ```bash
   kubectl run busybox --image=busybox --restart=Never -- /bin/sh
   ```
   Example:
   ```bash
   kubectl run busybox --image=busybox --restart=Never -- /bin/sh
   ```

---
