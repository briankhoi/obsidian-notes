**Minikube**
Testing local clusters on your machine can be difficult, so we use **minikube**, a tool where we have control plane and worker processes running on the same node with Docker pre-installed. minikube creates Virtual Box on your laptop and essentially generates a one node K8s cluster that runs in that Virtual Box.
- Virtualization & hypervisor are needed

**Kubectl**
A CLI tool that allows you to interact with a node's API server.

**Commands**
- minikube start
	- starts the local K8s cluster
- kubectl get nodes
	- lists the nodes in the K8s cluster
- minikube status
	- see status of host, kubelet, apiserver, and kubeconfig
- kubectl version
	- see version of client and server version of K8s is
- kubectl get pod
	- displays the list of pods
	- also kubectl get pod -o wide to get more information
- kubectl get services
	- displays the list of services
- kubectl cmd -h 
	- displays help/usage info on cmd
- kubectl create (type of kubernetes component) (flags)
	- do kubectl create -h for more info, then for specifics use --help, so like kubectl create deployment --help for specific deployment flags
	- we did kubectl create deployment nginx-depl --image=nginx which created a new deployment named nginx-depl and used the nginx image as the base
		- now, doing kubectl get pod shows the nginx pod created from the deployment
- kubectl get deployment
	- displays the list of deployments
	- kubectl get deployment (deeployment name) -o yaml to get deployment in yaml format
- kubectl edit (resource) (options)
	- try -h on this
	- in the tutorial, we edited our deployment by calling **kubectl edit deployment nginx-depl** and changed the image of nginx from "nginx" to "nginx:1.16". This caused the pod to delete itself and be replaced by a new pod with the new image, which is why we can see the older pod terminating
		![[Pasted image 20250403212427.png]]
		- because of this, the old pod's replica set also got deleted since in kubernetes, we only handle the deployments and K8s handles the rest
			![[Pasted image 20250403212531.png]]
- kubectl logs (podname)
	- shows you the logs inside of a pod for debugging
- kubectl describe (resource) (resource name)
	- shows comprehensive stats about the resource and events within it
	- like kubectl describe pod (podname)
	- kubectl describe sevice (servicename)
- kubectl exec (podname)
	- use the -it flag for "interactive terminal"
	- we did kubectl exec -it (mongo db podname) -- bin/bash to open a bash terminal in the pod
- kubectl delete (resource) (resource name)
	- like kubectl delete deployment nginx-depl
- minikube stop
	- stops the minikube K8s cluster
- for kubectl get (resource) you can also add --watch to watch it for updates

**Summary**
Overall, kubectl is a good way to interact with the K8s cluster API server. However, doing this in practice is typically super tedious/inefficient, so we usually have config yaml files that we program and then load using **kubectl apply -f (file.yaml)**
![[Pasted image 20250403214951.png]]
In the example above, we are specifying an nginx deployment. As we know, deployments are the "blueprint" to recreating pods automatically. This blueprint portion is listed under the template label, where we specify the properties of the pod we want the deployment to create.

After applying this deployment/manifest, we can edit the actual yaml file for any changes we may want to make, save it, and then apply the manifest again. kubectl will recognize the small changes and configure them accordingly, in theory by replacing the pods with the new ones with new changes loaded.

To check that the service is connected to a pod, do kubectl describe service (service name) and look at the endpoint IP addresses and make sure they match with the pods in kubectl get pod -o wide