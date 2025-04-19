K8s does not give you data persistence out of the box, so if you save data to a MySQL pod and the pod restarts, all the data there is lost. Thus, we need storage that does not depend on the pod lifecycle that must be available on all nodes and which needs to survive even if the entire cluster crashes.

This is where **persistent volumes (PV)** come in. They are a cluster resource created via a YAML file with `kind: Persistentolume`. Since we need this storage to be reliable and persistent, it needs actual physical storage like a local disk on the cluster or a remote storage or cloud storage. You can also have multiple types of storage in your cluster too like a blend of all three types; you don't need to stick to one.

In persistent volume YAML files, the spec attributes are often different and dependent on storage type (can be found in documentation of the storage you want to use)

Network File System (NFS) Storage Example:
![[Pasted image 20250410152340.png]]

Google Cloud Storage Example:
![[Pasted image 20250410152419.png]]

Local Storage Example:
![[Pasted image 20250410152544.png]]

Note: Persistent Volumes are not namespaced, meaning that hey are accessible to the whole cluster and all the namespaces.

**Local vs. Remote Volume Types**
Local volume types violate requirements #2 and #3 for data persistence, which are:
- not being tied to 1 specific node
- not surviving cluster crashes
As a result, we almost always use remote storage

**K8s Roles**
- K8s Admin: sets up and maintains the cluster
	- provisions and creates the storage resource
- K8s User: deploys the applications in the cluster
	- configures the application to claim the persistent volume

**Persistent Volume Claim**
The application has to be configured to use the persistent volume components. This is done through the resource `kind: Persistent Volume Claim` (PVC). The way is works is that PVC claims a volume with a specific storage size and capacity and other characteristics. Then, whichever persistent volume satisfies the claim/criteria is used.

In addition, the PVC must also be included in the Pods configuration so that all the containers in the pod have access to the PV storage. The container must also be mounted with the PV (notice how there's two fields for it in the image below).

![[Pasted image 20250410153357.png]]

PVCs must exist in the same namespace as the application using the claim (remember that PV is not in a namespace but PVCs are)

You can also use ConfigMap values in PV and PVC config files! 

A pod can use different volume types simultaneously. Note in the example we have different types of storage mounted but at differed places.
![[Pasted image 20250411010033.png]]

**Storage Class**
Storage classes provisions PVs dynamically when PVC claims it. They can be configured via a YAML file.
![[Pasted image 20250411010149.png]]

PVC claims it via their config:
![[Pasted image 20250411011041.png]]

Overall Workflow:
![[Pasted image 20250411011120.png]]
