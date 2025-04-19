**Worker Nodes**: Worker nodes/machine in a K8s cluster 
- Each node has multiple Pods on it
- Either a physical or virtual worker machine that runs multiple pods
- 3 processes must be installed on every Node
	- Kubelet: A process that interacts with both the container and the node
		- Responsible for taking the node configuration and starting a pod with a container inside, and assigning resources from the node to the container
	- Kube Proxy: Forwards the requests between pods
	- Container runtime (like Docker)
- Nodes do the actual work/pods have to run on nodes
- Nodes communicate via Services

**Control Plane Nodes**: Controls the cluster state and worker nodes
- 4 processes run on every node
	- API server: A cluster gateway which gets the initial request from the client of any updates/queries
		- Also ensures only authorized requests get through the cluster
		- Examples requests are like querying the health status of the cluster or to schedule a new pod
	- Scheduler: Decides where the next pod/component will be scheduled
		- Looks at the request and see how many resources the request needs, then looks at the current resource usage of nodes and decides which Node to schedule the new Pod on
		- Also note that the scheduler just **decides** on *where* to place the new Pod, it doesn't actually do it. The Kubelet is the one responsible for actually creating the new Pod. 
	- Controller Manager: Detects cluster state changes
		- When pods crash, the controller manager detects them and tries to recover the state as soon as possible so it invokes the scheduler, which then calls the Kubelet on the worker node to restart the pods
	- etcd: Key Value store of a cluster state (the cluster's brain!)
		- Cluster changes get stored in the key value store
		- Holds state data like resources and health, so when other processes like the scheduler check the resource allocation of different nodes, it gets that data from etcd
		- Actual application data like DB data is NOT stored in etcd, just cluster state information
- In practice, you will have multiple control plane nodes, with etcd forming a distributed storage across all control plane nodes

**To add a new Control Plane/Worker Node Server:**
- Get a new bare server
- Install all the control plane/worker node processes
- Add it to the K8s cluster