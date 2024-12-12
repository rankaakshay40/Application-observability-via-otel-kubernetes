# Application-observability-via-otel-kubernetes

make a folder named **App** and put the python application files in that
python-configmap.yaml
python-deployment.yaml
python-service.yaml

Now run the command in the **App**

kubectl apply -f .

This will create the configmap, deployment, service for the application. Ultimately it will create the pod for our application.

Run the command:

kubectl get all 

to check that they are properly deployed and now run the port-forward command to see the application is running on browser.

kubectl port-forward svc/single-app-single-collector 8000:8000

Check on browser 127.0.0.1:8000/docs



Now we will install the node exporter to collect infrastructure metrics:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 

helm repo update

helm install node-exporter prometheus-community/prometheus-node-exporter

Now to see the node exporter is giving metrics we will use port-forward command again


kubectl port-forward svc/node-exporter-prometheus-node-exporter 9100

Check on browser: 127.0.0.1/metrics and we will see the metrics


Now make a folder named *Opentelemetry* inside that we should have our otel-config file 

Now run the command to deploy our opentelemtry collector pod inside our *Opentelemtry* folder


kubectl apply -f .

This will create the pod up and running

Now check the logs for both the application and otel-pod if are facing any error. 

Now run the port forward command for application again to see our agent is forwarding the logs, metrics and traces to the endpoints

kubectl port-forward svc/single-app-single-collector 8000:8000

Check on browser 127.0.0.1:8000/docs

Check via curl command also

