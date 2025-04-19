A namespace is a mechanism/container to hold things like functions, classes, and constraints under a unique name to avoid name collisions.

By default, we have 4 namespaces that Kubernetes offers:
- kubernetes-dashboard (only with minikube)
- kube-system
	- for system processes like master or kubectl processes
	- do NOT create or modify anything in kube-system
- kube-public
	- holds publicly accessible data
	- a configmap which contains cluster information
- kube-node-lease
	- holds the "heartbeats" of nodes
	- each node has associated lease object in this namespace which contains info about their availability
- default
	- resources you create are located here

To create a namespace, use `kubectl create namespace (namespace name)`. You can also do it through a YAML configuration file
```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
	name: mysql-configmap
	namespace: my-namespace
data:
	db_url: mysql-service.database
```

To view the resources in a namespace, run `kubectl get (resource) -n (namespace name)`

To apply a configuration file in a namespace, you can either declare a namespace field in the file itself or run `kubectl apply -f (filename).yaml --namespace=(namespace name)`

To change the activate namespace you're working with so you don't have to add the flag every time, you can download a tool named **kubens** and run `kubens (namespace name you want to switch to)` and `kubens` to see which namespace you're in, as well as the list of namespaces

**Use Case for Namespaces**
Why should we even create new namespaces instead of just cramming everything in default?

As YAML files and resources fill up in the default namespace it gets hard to see what's actually in there/an overview of it. Also, if you have two resources with the same name, the one that gets applied last will override the initial one, potentially causing issues and losing work/source code.

To solve this, we can do things like making database and monitoring namespaces so that we can group related resources into their associated namespaces so that it's easier to manage as things scale.

Here are more use-cases:
- Allowing shared resources for staging and production environments within the same cluster like nginx-ingress controller.
- Supporting blue/green deployment for your application (sharing resources between two production versions, one version is the current and the other is the upcoming release).
- Limit access on namespaces when working with teams, so you can namespaces editable by only teams who are authorized to work on it so there are no interferences or accidental conflicts between teams. 
- You can also limit the amount of resources (resource quotas) that a namespace is able to use. Useful if one namespaces uses too many resources which affects other namespaces from functioning properly

**Namespace Tradeoffs**
- You can't access most resources from another namespace (A ConfigMap from project A can't be used in ConfigMap from project B even if it's the same reference)
- However, you can access services in another namespace. The way this works is that in a ConfigMap url, in addition to the service name you add a "." as a delimeter and then add the namespace name. So you can do something like `db_url: mysql-service.database` where mysql-service is the service in another namespace, and database is the name of that namespace
- There are some components which can't be created within a namespace such as persistent volume and nodes. These live globally in a cluster and cannot be isolated.
	- You can view the resources not in a namespace with: kubectl api-resources --namespaced=false. Change the flag value to true to see the resources in a namespace.

