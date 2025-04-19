Helm is a package manager for K8s. A helm chart is a package that contains all the necessary resource definitions and configurations to deploy an application, tool, or service onto a Kubernetes cluster. 

To search for helm packages, you can run `helm search <keyword>` or go on Helm Hub.

Helm also works as a templating engine, so if you have like similar microservices or something, instead of using basically the same YAML files but with a few lines changed for the different name, Helm allows you to define a template file which serves as a blueprint but with dynamic values taken in from an external configuration file named values.yaml. So now you just have one file, which is practical for efficiency in general and in CI/CD where you can replace values on the fly.
![[Pasted image 20250409235219.png]]
This is also useful when you deploy the same application across different K8s clusters like dev, staging, prod.

**Helm Chart Structure**
```
mychart/
	Chart.yaml
	values.yaml
	charts/
	templates
```
- Top level mychart folder: the name of the chart
- Chart.yaml: meta info about the chart
- values.yaml: values for the template files
- charts folder: chart dependencies
- templates folder: the actual template files

To install a chart, run `helm install <chartname>`.

You can also override/merge values. So for example, if you want to add on to a values.yaml in a package like a version number, you make your own values file so like `my-values.yaml`, then merge it like `helm install --values=my-values.yaml <chartname>` which overrides or adds the new values and results in a new `.Values` object. You can also do `helm install --set <attribute>=<new value>` but as always, files are better practice.

**Installation**
The helm version comes in two parts:
- Client (Helm CLI)
- Server (Tiller)

When you deploy a helm chart using helm install, the helm client sends the requests to Tiller which runs in the cluster. Then, Tiller executes these commands within the cluster.

Tiller:
Tiller keeps track of the history of all chart executions like upgrades, so changes are applied to the existing deployment instead of creating a new one. This is also useful if a new update causes a crash, you can just rollback.

Tiller Downsides:
- Tiller has too much power inside of the K8s cluster, which leads to some security issues.
- So now, in Helm 3 Tiller was removed and it's just a simple Helm binary now, but it's still an important distinction to make since both versions of Helm are still in-use/popular.

Relevant commands:
- `helm install <chartname>`
- `helm upgrade <chartname>`
- `helm rollback <chartname>`