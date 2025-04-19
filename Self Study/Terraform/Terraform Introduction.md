Based on: https://www.youtube.com/watch?v=l5k1ai_GBDE&ab_channel=TechWorldwithNana
## What is Terraform?
Terraform is an Infrastructure as Code (IaC) tool for automated infrastructure provisioning. It utilizes declarative language, where you define WHAT end result you want, not the exact steps.
## Ansible vs. Terraform
Ansible and Terraform are both IaC tools commonly associated with each other, but are different. Developers use a mix of both, using Terraform for provisioning infrastructure, and using Ansible  for configuring that infrastructure.
## What is Terraform Used For?
Terraform is used for:
- Replicating infrastructure 
- You can replicate infrastructure for identifical staging, production, and development environments
- Automate continuous infraustructure chanes

Terraform Architecture
Terraform is centered around two main components:
- Core
	- takes in Tf-Config which we write and define what needs to be created or provisioned
	- State
		- keeps up to date state of setup of what the infrasutrcutre looks like
	- cores takes in the inputs and compares current state vs desired state and plans what needs to be created/updated/destroyed
	- core creates an execution plan above and uses providers to execute the plan connect to those platforms and carry out those execution stes
providers
- AWS, Azure | IaaS
- Kubernetes PaaS platform as a service
- Fastly Software as a service
over a hundred providers 
each provider gives terraform access to resources
e.g. aws provider gives access to resources like ec two
or kubernetes to deployments services

example
![[Pasted image 20250217220513.png]]
we have a kuberentes provider and create a kubernetes namespace resource as an attribute


declaarative vs aimperative
declarative you specify end state 
like you awnt 7 states with this fireawll config with this user with the following permissions, terraform figures out what needs to be done and calculates it
imperative you specify the steps you want to take/instructions like remove two serevices add fireawll config add permissions to aws user

terraform basic commands
- refresh: query infra provider to get current state of provider
- plan: create an execution plan
- apply: execute the plan
if you want to execute the config file you run apply as it runs refresh first, then plan, then apply
- destroy: destroy the resources/infra (like after a demo or smt)
	
