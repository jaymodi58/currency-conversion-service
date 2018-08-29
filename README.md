# currency-conversion-service
1. forex-service
2. currency-conversion-service
3. eureka-naming-server
4. config-server

## How to run:
1. Redirect command prompt to forex-service directory.
2. Generate the docker image.```mvn clean install```
3. Required to reach out docker of minikube.  
	```@FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i```
4. Verify whether the docker image is in the list or not.  
	```docker images```
5. (Optional) To deploy it locally:
    - `docker run -p 8000:8000 us.gcr.io/currency-exchnage-214419/forex-service`

	- Open browser and go to <docker-machine-ip>:8000/currency-exchange/from/USD/to/INR
	- To stop the container: `docker rm <container-id>`
	    - To find out the container id: `docker ps [-a]`
	- To remove the container: `docker rm <container-id>`

6. Push the docker image to Google Cloud Registry(GCR).  
    `docker us.gcr.io/currency-exchnage-214419/forex-service`  
	For more information: [Using Google Container Registry with Kubernetes](https://cloud.google.com/container-registry/docs/pushing-and-pulling)
7. Change the directory.  
	`cd ..`
8. Two Options:
	#### Deploy the application on local cluster (MiniKube).
	1. You need to authenticate Google Cloud Registry by creating secret in minikube.
		```
		kubectl create secret docker-registry gcr-json-key \
        --docker-server=us.gcr.io \
		--docker-username=_json_key \ 
		--docker-password="$(cat 'C:\Users\Jay Modi\Downloads\currency-exchnage-214419-1c4337ec78a1.json')" \
		--docker-email=jay.modi58@gmail.com
		```

	2. Path the secret to the service account.
		```
		kubectl patch serviceaccount default \
		-p '{"imagePullSecrets": [{"name": "gcr-json-key"}]}'
		```
		Please follow: [Pushing and Pulling Images](https://container-solutions.com/using-google-container-registry-with-kubernetes/)

	3. Create the deployment using deployment.yaml.  
        `kubectl create -f deployment.yaml`

	4. Create Service (Expose the deployment to the internet.)
		- Option I: Using command-line.  
		    `kubectl expose deployment currency-exchange --type=LoadBalancer --port 8000 --target-port 8000`
            
		- Option II: Using service.yaml  
		    `kubectl create -f deployment.yaml`
	5. Call the service.
	    - Option I: `minikube service currency-exchange`  
		It will redirect it to browser. Call the rest api as per is defind.
        
		- Option II: `minikube service currency-exchange --url`  
		Call the rest api as per defination.
	6. To clean up.  
		Delete the service:`kubectl delete service currency-exchange`  
		Delete the deployment: `kubectl delete deployment currency-exchange`

	#### Deploy the application on Google Kubernetes Cluster.
    Either you can use [gcloud](https://cloud.google.com/sdk/gcloud/) locally or you can open [Google Cloud Shell](https://cloud.google.com/shell/).
	
	1. Connect to the cluster first.  
		`gcloud container clusters get-credentials currency-exchange-cluster --zone us-east4-a --project currency-exchnage-214419`
    
	2. Create deployment.  
		`kubectl apply -f https://raw.githubusercontent.com/jaymodi58/currency-conversion-service/master/deployment.yaml`
    
	3. Create service.  
		`kubectl apply -f https://raw.githubusercontent.com/jaymodi58/currency-conversion-service/master/service.yaml`
	
	4. Get running service details.  
		`kubectl get services`
		**OR**
		You can use Kubernetes UI from the google account. All you need is external ip and port to reach out.
