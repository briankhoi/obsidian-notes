In this demo project, we will be creating this system through the lens of K8s
![[Pasted image 20250409200257.png]]

First, we create the MongoDB pod (bottom right of diagram). We need to have a basic YAML deployment that we need to configure for Mongo:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo
```
To help us in configurating this deployment, we go to the mongo image documentation to see specific details like ports that mongo uses: https://hub.docker.com/_/mongo

Also note this file will be checked into a repository, so when defining a value for mongo's environment variables of username and password, we wouldn't type it in the yaml file itself, but rather use K8s Secrets to store them

**Creating a Secret**
When creating a secret, the value must be base64 encoded. To do this, go to your terminal and run
`echo -n 'string value' | base64`, then copy it to your Secret configuration file as a value.

mongo-secret.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  mongo-root-username: dXNlcm5hbWU=
  mongo-root-password: cGFzc3dvcmQ=
```

Now, apply your secret `kubectl apply -f mongo-secret.yaml` and run `kubectl get secret` to double check. 

Now that we have created a secret, we can reference it within our deployment file. Below the name of the environmental variables, instead of writing a value field, we use the valueFrom field like so:
```yaml
valueFrom:
	secretKeyRef:
		name: mongo-secret
		key: mongo-root-password
```

Now that your deployment is done, apply it with `kubectl apply -f mongo.yaml`

**MongoDB Internal Service**
Now, we will create the MongoDB Internal Service so that other services or other pods can talk to our MongoDB pod.

In YAML, we can use three dashes --- to separate a file. We do this for our mongo.yaml to add the service too because deployment and service are typically in the same file because they belong together.

So now we have this file and re-apply the yaml file (notice how it says the deployment is unchanged and service is created)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo
          ports:
            - containerPort: 27017
          env:
            - name: MONGO_INITDB_ROOT_USERNAME
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-root-username
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```

Now looking at the Docker hub page: https://hub.docker.com/_/mongo-express, we see that we need to include what database address to connect to and the credentials to do so.

These are:
- ME_CONFIG_MONGODB_ADMINUSERNAME
- ME_CONFIG_MONGODB_ADMINPASSWORD
- ME_CONFIG_MONGODB_SERVER   

For the credentials, we tie those to the secrets file from earlier. For the server, we use a config map to put in the value of the mongo db server address.

Config Map:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-configmap
data:
  database_url: mongodb-service
```

Inserting it back into the deployment and full deployment for mongo express
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
        - name: mongo-express
          image: mongo-express
          ports:
            - containerPort: 8081
          env:
            - name: ME_CONFIG_MONGODB_SERVER
              valueFrom:
                configMapKeyRef:
                  name: mongo-configmap
                  key: database_url
            - name: ME_CONFIG_MONGODB_ADMINUSERNAME
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-root-username
            - name: ME_CONFIG_MONGODB_ADMINPASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-root-password
```

make an external selector by adding a type field in the spec of "LoadBalancer"
- this accepts an external IP address and accepts external requests
also in ports section add a nodePort field which is the port accessed from the outside needed for a browser, must be between 30000-32767


kubectl get service
clusterIP gives it an internal IP address
loadbalance also gives it one, but load balancer also gives it an external IP address

minikube mongo-express-service
