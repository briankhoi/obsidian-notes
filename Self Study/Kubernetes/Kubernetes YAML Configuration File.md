Here is a basic sample YAML file for reference:
![[Pasted image 20250409000821.png]]

There are three parts of a K8s configuration:
- Metadata
	- Example is in line 3 of the above image, under the "metadata" header we list out metadata about the deployment
- Specification
	- Example is in line 7 of the above image, under the "spec" header we list out different attributes that our deployment has
	- Attributes inside the spec are specific to the **kind** of the configuration file (line 2 in the image). In the example, it is a deployment so we can specify things like replicas. However, if it was changed to a service, we can specify attributes like ports
- Status
	- .An automatically generated component by K8s. Essentially, Kubernetes looks at your resource and if it notices that the actual status is different than the desired status specified in the configuration file, K8s will try to self-heal and add a new "status" field with a timestamp of the current status of the resource it sees, and continually try to make it match the desired state.
	- The current status of the resource is obtained through **etcd**, as that service holds the status information of all K8s components

Also, note that the first two lines in the file (apiVersion and kind) are for you to specify at a super high level what you want to create and with what API version.

YAML also is very strict with syntax - indentation really matters!

You can do kubectl get deployment (deployment name) -o yaml

**Template**
A template can be an attribute under the specifications section. The configuration defined under the template header serves as the blueprint of a Pod
![[Pasted image 20250409164752.png]]

**Labels and Selectors**
![[Pasted image 20250409172805.png]]
Labels are defined the metadata section while selectors are defined in specifications.

When creating a deployment, you want to connect the pods to the deployment so that the deployment knows which pods belong to it. So, this is where labels come into play. By specifying a label, in our case in the metadata field of the Pod (so inside the template header), we assign a tag to the pod. Then, in the selectors, we use matchLabel which helps the selector connect the pod to deployment.

Moving onto the top metadata field of the actual deployment, this is used to connect a deployment to a service.

**Ports in Service and Pod**
In the deployment, we specify a containerPort field for what port the container will be listening on. Then, in the service connected to the deployment, we specify the protocol, port, and targetPort. The port is what port the service connected to the deployment listens to, and the targetPort is the port of the pod that the deployment service wants to forward the request to
![[Pasted image 20250409180105.png]]
 