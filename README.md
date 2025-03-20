Kubernetes Install MetalLB Loadbalancer
Kubernetes Install MetalLB Loadbalancer. We look at installing MetalLB in a bare-metal Kubernetes cluster, including testing with Nginx pod

MetalLB provides a bare-metal load balancer
MetalLB is a freely available, open-source solution that addresses the problem described above with Kubernetes load balancers for bare-metal clusters. Even though it is open-source and free, many are using it in production and have had great success in doing so.

It offers a solution to offer a network loa balancer implementation that integrates with standard networking environments where bare-metal Kubernetes clusters are found. The implementation is straightforward and is meant to “just work.”

MetalLB requirements
The requirements for running MetalLB in your Kubernetes cluster are the following:

A Kubernetes cluster, running Kubernetes 1.13.0 or later
No other network load-balancing functionality enabled
A cluster network configuration that can coexist with MetalLB
Some IPv4 addresses for MetalLB to hand out
When using the BGP operating mode, you will need one or more routers capable of speaking BGP
When using the L2 operating mode, traffic on port 7946 (TCP & UDP, other ports can be configured) must be allowed between nodes, as required by members

MetalLB installation instructions for Legacy versions
To begin with, I am installing MetalLB using the Manifests approach. To install MetalLB using Kubernetes manifest, use the following lines. I am simply following the installation documentation found here.

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.5/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.5/manifests/metallb.yaml

Create a Config Map for MetalLB
Once you have deployed MetalLB, you need to follow the documentation to deploy a Config Map. The config map is what determines the MetalLB network configuration and what IPs it hands out to services.

Below is simply the code copied from the documentation here. The only thing I am changing is the addresses section to match my local network. Paste the code into a temporary YAML file you can stick somewhere.

After you have the YAML file created and ready, we can deploy it using:

kubectl create -f /tmp/metallb.yaml

Testing your MetalLB configuration deploying Nginx
Now that we have installed MetalLB and created the config map for the network configuration it will hand out, we should be able to test that MetalLB works correctly. Let’s use an Nginx container deployment to test the handing out of IP addresses from MetalLB.

To deploy a test Nginx pod, you can use the following command:

kubectl create deploy nginx --image nginx:latest
You can then look at the deployment with:

kubectl get all

Exposing the Nginx deployment with type LoadBalancer
Now that we have deployed an Nginx test pod, we can expose the deployment using the type LoadBalancer.

kubectl expose deploy nginx --port 80 --type LoadBalancer

Using the kubectl get svc command, we can see the External IP is correctly assigned from the MetalLB IP pool. 
