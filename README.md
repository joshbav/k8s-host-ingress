### HOST HEADER INGRESS ROUTING TUTORIAL 
Revision 10-26-18

This tutorial will show how to do host based routing using the Traefik ingress controller. This demo assumes there is only 1 K8s public node. It is simplified to route to a single deployment based on its host header. 

Here’s the
[Traefik docs](https://docs.traefik.io/basics/)
which explain the Traefik architecture. Ignore everything beyond the Entrypoints section since it’s not for a Kubernetes configuration. 

Incoming requests end on entrypoints, as the name suggests, they are the network entry points into Traefik (front side listening port, SSL, traffic redirection, etc).

Traffic is then forwarded to a matching frontend. A frontend defines routes from entrypoints to backends. Routes are created using requests fields (Host, Path, Headers...) and can match or not a request. This tutorial uses a Host header.

The frontend will then send the request to a backend. A backend can be composed by one or more servers, and by a load-balancing strategy.

Here’s the 
[Traefik docs](https://docs.traefik.io/user-guide/kubernetes/)
for Kubernetes for the full cheese demo. The demo here is a simplified version of this since it’s an ideal starting point. We will have NGINX and Apache as our two apps, accessed via two host headers. You can use these examples as a simple template (however more settings are needed for production). 

#### Edit /etc/hosts to bypass DNS

To bypass DNS, edit your /etc/hosts file, add the following entries:

`<K8s public node IP>  www.nginx.test`   
`<same K8s public node IP> www.apache.test`

Host headers will now be sent when we browse to those DNS FQDNs. Traefik will then use those host headers to route to the proper backend.

#### Install traefik

`kubectl create -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/traefik.yaml`

#### Install Apache deployment

`kubectl create -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/apache.yaml`

#### Install NGINX deployment

`kubectl create -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/nginx.yaml`

#### Browser test

Wait 30 seconds, most of this is the container pull from Docker Hub.

Open browser to www.nginx.test
Should show default NGINX page.

Open browser to www.apache.test
It should show default Apache page (“it works”).

#### Uninstall all of it

`kubectl delete -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/apache.yaml
kubectl delete -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/nginx.yaml
kubectl delete -f https://raw.githubusercontent.com/joshbav/dcos-demo1/master/traefik.yaml`

#### Remove the entries from your /etc/hosts file

Simply delete the two entries that were added earlier.
