A simple web-app which displays "Hello World! Hostname: <app_container/node>" when accessed.

This code uses the Flask web framework to create a web server that listens on port 80.

The App deployment in Cloud uses Kubernetes as Orchestration Engine and Docker for Containerization of the App.

This implementation was done on Digitalocean Kubernetes(DOKS) but is generic and can be implemented on other Cloud platforms as well.

A Kubernetes Service load-balancer and horizontal pod autocaling is used in HA environment.

Steps for the building the Docker image and deploying the App:

To build the Docker image for the App using the Dockerfile(Dockerfile should be present in same dir from where we are running the command): docker build -t doweb-app .

Check if the image got created successfully: docker images

Tag the image with the FQDN of the registry where we plan to push/upload it: docker tag my-python-app registry.digitalocean.com/<your-registry-name>/doweb-app

Push the image to registry: docker push registry.digitalocean.com/<your-registry-name>/doweb-app

Create a Kubernetes cluster to deploy the App. This can be done from the UI/Control Panel, or using the CLI as well:
doctl kubernetes cluster create <your-cluster-name> --tag do-tutorial --auto-upgrade=true --node-pool "name=mypool;count=2;auto-scale=true;min-nodes=1;max-nodes=3;tag=do-tutorial"

After the cluster is created and ready, authorize your cluster to use your private registry so it can download images:
doctl registry kubernetes-manifest | kubectl apply -f -

Next, configure Kubernetes to use this secret as an authentication token when pulling images from your private registry:
kubectl patch serviceaccount default -p '{"imagePullSecrets": [{"name": "registry-<your-registry-name>"}]}'

Now we are ready to create/deploy the Apps in the cluster. For this, we will be using the declarative approach and use manifests file to create a deployment and load-balancer service. The manifest files have been added to the repo for reference: kubectl create -f .

Once the Deployment is up and running, we create HPA(horizontal pod autoscaling) to allow for increase in the number of pods when the load is higher(here we have selected CPU as metric, we can add memory, disk space etc as required): kubectl autoscale deployment <deployment_name> --cpu-percent=50 --min=1 --max=5

Note: Please ensure that metrics server is installed on the K8s cluster.
