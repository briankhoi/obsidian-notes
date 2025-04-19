To help connect our service to the outside world, instead of using an External Service, which opens our IP address and port (not too secure in production), we use an Ingress resource, which acts as a layer between the browser and internal service to forward traffic into the cluster and results in a system with no exposed IP address or port.

Example:
![[Pasted image 20250409222939.png]]

The rules field here represents routing rules.
- Host: the host that the user enters in the browser/IP address of the entrypoint of the node into the cluster
- HTTP: the protocol used to forward request from host to Ingress
	- Paths: used to define rules for the path in the host URL, so everything after the last / in https://my-app.com/
- serviceName: the internal service name which receives the request from browser 

**Configuring Ingress**
It's not enough to just have an Ingress configuration, you also need an implementation for Ingress called an **Ingress Controller** which evaluates and processes ingress rules. There are many third-party implementations of this controller, including the K8s built-in K8s Nginx Ingress Controller

![[Pasted image 20250409223653.png]]
 A lot of the times though, there are K8s environments like Google Kubernetes Environment (GKE) that give you a Cloud Load Balancer as an entry point. But if you're doing a bare K8s cluster by yourself you need to configure an entry point either inside of the cluster of outside as a separate server like a proxy server.

**Installing Ingress Controller in Minikube**
To use ingress with minikube, run `minikube addons enable ingress`. This starts and configures the K8s Nginx Ingress controller for you.

Then, create an ingress rule. For this example, we'll create an ingress rule for our kubernetes-dashboard

Run `kubectl get all -n kubernetes-dashboard` to get the serviceName and port for your configuration file. Then, the configuration file defining an ingress rule between a website dashboard.com and the internal kubernetes-dashboard service looks like:
![[Pasted image 20250409225849.png]]

**Default Backend**
When a request comes into the K8s cluster that is not mapped to the backend/service, the default backend is used to handle the request. It can be viewed with `kubectl describe ingress (ingress name)`. A good use-case for this is setting the default backend to a not found page, as requests to a URL not supported by the cluster like example.com/invalidpage maps to a 404 not found page re-directing the user to another page.

**Ingress Use-Cases**
- Multiple paths for the same host like setting the host to myapp.com and having different paths of /analytics or /shopping for the different services
- Multiple sub-domains or domains so instead of having one host with multiple paths, you can have multiple hosts with one path like analytics.myapp.com and shopping.myapp.com as the hosts
- Configuring TLS certificates
![[Pasted image 20250409230754.png]]
