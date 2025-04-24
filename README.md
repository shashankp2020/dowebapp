A simple web-app which displays "Hello World! Hostname: <app_container/node>" when accessed.
This code uses the Flask web framework to create a web server that listens on port 80.
The App deployment in Cloud uses Kubernetes as Orchestration Engine and Docker for Containerization of the App.
A Kubernetes Service load-balancer and horizontal pod autocaling is used in HA environment.
