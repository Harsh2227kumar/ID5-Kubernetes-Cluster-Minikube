🧱 Steps We Performed
---------------------

### **1\. Installed Docker**

**Why:**Docker provides the container runtime that Kubernetes (via Minikube) uses to run pods/containers.

**What we did:**

`sudo apt install docker-ce`

**What it achieved:**Allowed Minikube to use Docker as its internal virtualization driver.

**Logic:**Minikube spins up a Kubernetes node by running a lightweight VM or container, and Docker helps launch and manage that environment.

### **2\. Installed kubectl**

**Why:**kubectl is the command-line interface to interact with your Kubernetes cluster.

**What we did:**

`curl -LO ...
chmod +x kubectl
sudo mv kubectl /usr/local/bin/`
**What it achieved:**Gave us the ability to deploy, scale, and inspect Kubernetes resources like pods, services, and deployments.

**Logic:**kubectl sends commands to the Kubernetes API server running inside Minikube.

### **3\. Installed Minikube**

**Why:**Minikube sets up a full single-node Kubernetes cluster locally (even on VMs/cloud).

**What we did:**

`curl -LO ...
chmod +x minikube
sudo mv ...
minikube start --driver=docker`

**What it achieved:**Bootstrapped a local Kubernetes environment inside the droplet using Docker.

**Logic:**Minikube acts like a lightweight Kubernetes orchestrator on non-clustered systems.

### **4\. Created a Deployment (deployment.yaml)**

**Why:**A Deployment tells Kubernetes how many pods to run and with which container image.

**What we did:**

yaml
`kind: Deployment
image: nginx
replicas: 1`

Applied it:

`kubectl apply -f deployment.yaml`

**What it achieved:**Launched a pod running nginx web server inside the Minikube cluster.

**Logic:**The Deployment controller ensures the desired number of pods (replicas) are always running.

### **5\. Created a Service (service.yaml)**

**Why:**To expose our nginx pod so that it's reachable over the network.

**What we did:**

*   Tried NodePort first
    
*   Switched to LoadBalancer
    

`kind: Service
type: LoadBalancer`

**What it achieved:**Exposed port 80 of the nginx pod and made it routable through Minikube’s LoadBalancer emulation.

**Logic:**In a real cloud setup, LoadBalancer would use a cloud provider’s external LB. With Minikube, we emulate this using minikube tunnel.

### **6\. Ran minikube tunnel**

**Why:**To simulate a cloud LoadBalancer and route traffic from outside into the service.

**What we did:**

`sudo minikube tunnel`

**What it achieved:**Created a route from the droplet’s internal network to the Kubernetes service.

**Logic:**Minikube intercepts requests to the LoadBalancer IP and forwards them to the right pod.

### **7\. Used kubectl port-forward**

**Why:**To bypass private/internal IPs and expose the app on the droplet’s localhost.

**What we did:**

`kubectl port-forward service/my-service 8080:80`

**What it achieved:**Mapped droplet’s localhost:8080 to the Kubernetes pod’s :80.

**Logic:**Port-forward tunnels traffic from your local machine through kubectl into the pod.

### **8\. Used SSH tunnel for remote access**

**Why:**Because DigitalOcean droplets don’t expose Minikube ports directly.

**What we did:**

`ssh -L 8080:localhost:8080 root@<droplet-ip>`

**What it achieved:**Allowed us to visit the Kubernetes app in a **browser on our local machine**:

`http://localhost:8080`

**Logic:**SSH forwards a port from your local machine to the droplet, which is forwarding it into Kubernetes.

🧠 Final Outcome
----------------

ComponentStatusResultKubernetes Setup✅ CompleteWorking local cluster in MinikubeApp Deployment✅ DoneNGINX app running in podsService Exposure✅ VerifiedKubernetes LoadBalancer emulated and accessed externallySSH Tunnel✅ AppliedApp reachable via localhost on your own computer

🧩 What You Learned / Achieved
------------------------------

*   Full end-to-end Kubernetes deployment workflow on a non-cloud-native system
    
*   Working knowledge of:
    
    *   Deployments, Pods, Services
        
    *   Minikube internals and limitations
        
    *   Exposing services in cloud VM environments
        
*   Overcame the common DevOps challenge of **accessing services in a local cluster from the internet**
