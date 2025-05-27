**Pods**: The smallest unit of K8s
- An abstraction over a container so that we can eventually replace containers if we want to and so that we don't have to interface with Docker
- Usually one application per pod
- Each pod gets its own IP address
- New IP address on re-creation

**Services**:  A static/permanent IP address on each pod 
- Useful because if a pod dies and restarts, you don't have to keep changing IP addresses to keep accessing the pod due to the new pod having a different one
- The lifecycle of Pods and Services are NOT connected
- Services are also load balancers, forwarding requests to which pods are available

**Ingress**: Traffic outside the K8s cluster gets routed to Ingress, which routes it to the service

**ConfigMap**: An external configuration of the application
- Contains non-secret environment variables like MONGO_DB_URL
- Connected to the pod so that if you change the name of stuff in the ConfigMap, that's it the pod updates by itself you don't need to go through the whole cycle of pushing new changes, re-building it, etc.

**Secrets**: ConfigMap but for confidential data
- Used to store secret environment variables like API_KEY
- Data is base64 encoded (built-in security mechanism not enabled by default)
- Also connected to the pod so the pod can see the secret data and use it

**Volumes**: Persistent storage for pods
- If our pod goes down, the data is lost. We don't want this, so we attach volumes to the pod
- Volumes can either be storage on the local machine or remote, outside of the K8s cluster like cloud storage
- So now, once the pod goes down, the data is still there because its on local/remote storage via the volume
- K8s doesn't manage data persistence, developers are responsible for ensuring data is backed up, replicated, and kept on proper hardware

**Deployments**: Blueprint for creating pods
- Used for managing stateless pods
- Specify the number of replicas of a pod
- Can scale pods up and down depending on load
- Abstraction on top of pods
- CANNOT replicate stateful apps like databases

**StatefulSets**: Deployments but for stateFUL applications
- Same features as deployments but also makes sure DB read/writes are synchronized so no database inconsistencies are offered
- Deploying StatefulSets in practice are a bit tedious, so it's common practice to host DBs outside the K8s cluster

**ReplicaSet**: Manages the replicas of a Pod
![[Pasted image 20250403211955.png]]
Notice how the replicaset and pod share the same initial hash, and then the second hash part for the pod is the pod's unique name when created. 

**Custom Resource Definitions (CRDS)**: Kubernetes has a custom resource definitions functionality to definite custom configuration objects for a user's needed use case