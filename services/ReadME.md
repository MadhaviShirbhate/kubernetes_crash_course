Kubernetes services.

Kubernetes services enable communication between various components within and outside of the application.

Kubernetes Services helps us connect applications together with other applications or users.

For example, our application has groups of parts running various sections, such as a group for serving

a front end load to users and other group for running back end processes, and a third group connecting

to an external data source.

It is services that enable connectivity between these groups of parts.

Services enable the front end application to be made available to end users.

It helps communication between back end and front end parts and helps in establishing connectivity to

an external data source.

Thus, services enable loose coupling between micro services in our application.

Let's take a look at one use case of services.

let's say we  deployed our pod, having a web application running on it.

How do we, as an external user access the web page?

First of all, let's look at the existing setup.

The Kubernetes node has an IP address, and that is 192.1 60 8.1.2.

My laptop is on the same network as well, so it has an IP address.

192.1 68 1.10.

The internal pod network is in the range 10.2 40 4.0.0 and the port has an IP 10.2 40 4.0.2.

Clearly I cannot ping or access the port at address 10.2 44 0.2 as it's in a separate network.

So what are the options to see the web page?

First, if we were to search into the Kubernetes node at one 92.1, 68 .1.2 from the node, we would

be able to access the pod's web page by doing a curl.

Or if the node has a die, we would fire up a browser and see the web page in a browser following the

address.

HTTP 10.2 40 4.0.2.

But this is from inside the Kubernetes node, and that's not what I really want.

I want to be able to access the web server from my own laptop without having to switch into the node

and simply by accessing the IP of the Kubernetes node.

So we need something in the middle to help us map requests to the node from our laptop through the node

to the pod running the web container.

This is where the Kubernetes service comes into play.

The Kubernetes service is an object, just like parts replica set or deployments.

One of its use cases is to listen to a port on the node and forward requests on that port to a port

on the port running the web application.

This type of service is known as a node port service because the service listens to a port on the node

and forward requests to the port.

There are other kinds of services available which we will now discuss.

The first one is what we discussed already.

Node port, where the service makes an internal port accessible on a port on the node.

The second is cluster IP.

And in this case, the service creates a virtual IP inside the cluster to enable communication between

different services, such as a set of front and servers to a set of backend servers.

The third type is a load balancer, where it provisions a load balancer for our application in supported

cloud providers.

A good example of that would be to distribute load across the different web servers in your front end

tier.

We will now look at each of these in a bit more detail along with some demos.

the Node Port Kubernetes service.

Getting back to Node port, a few slides back we discussed about external access to the application.

We said that a service can help us by mapping a port on the node to a port on the port.

Let's take a closer look at the service.

If you look at it, there are three ports involved.

The port on the port where the actual web server is running is 80.

And it is referred to as the target port, because that is where the service forwards.

The request to the second port is the port on the service itself.

It is simply referred to as the port.

Remember, these terms are from the viewpoint of the service.

The service is in fact like a virtual server inside the node, inside the cluster, it has its own IP

address, and that IP address is called the cluster IP of the service.

And finally, we have the port on the node itself, which we use to access the web server externally,

and that is known as the node port.

As you can see, it is set to 30,008.

That is because node ports can only be in a valid range, which by default is from 30000 to 32767.

Let's now look at how to create the service, just like how we created a deployment replica set or pod

in the past.

We will use a definition file to create a service.

The high level structure of the file remains the same as before.

We have the API version, kind metadata and spec sections.

The API version is going to be v one.

The kind is, of course, service.

The metadata will have a name and that will be the name of the service.

It can have labels, but we don't need that for now.

Next we have spec and as always, this is the most crucial part of the file as this is where we will

be defining the actual services.

And this is the part of a definition file that differs between different objects.

In the spec section of a service, we have type and ports.

The type refers to the type of service we are creating.

As discussed before, it could be cluster IP, node, port or load balancer.

In this case, since we are creating a node port, we will set it as node port.

The next part of the spec is ports.

This is where we input information regarding what we discussed on the left side of the screen.

The first type of port is the target port, which we will set to 80.

The next one is simply port, which is the port on the service object, and we will set that to 80 as

well.

The third is node port, which we will set to 30,008 or any number in the valid range.

Remember that out of these the only mandatory field is port.

If you don't provide a target port, it is assumed to be the same as port.

And if you don't provide a known port, a free port in the valid range between 30,030 2000 767 is automatically

allocated.

Also note that ports is an array, so note the dash under the port section that indicates the first

element in the array.

You can have multiple such port mappings within a single service.

So we have all the information in, but something is really missing.

There is nothing here in the definition file that connects the service to the pod.

We have simply specified the target port, but we didn't mention the target port on which pod.

There could be hundreds of other pods with web services running on port 80.

So how do we do that?

As we did with the replica sets previously and a technique that you will see very often in Kubernetes.

We will use labels and selectors to link these together.

We know that the pod was created with a label.

We need to bring that label into the service definition file.

So we have a new property in the spec section and that is called selector, just like in the replica

set and deployment definition files under the selector provide a list of labels to identify the pod.

For this.

Refer to the pod definition file used to create the pod.

Pull the labels from the pod definition file and place it under the selector section.

This links the service to the pod.

Once done, create the service using the cube control.

Create command and input the service definition file.

And there you have the service created to see the created service run the cube control get services

command that lists the service, the cluster IP and the mapped ports.

The type is node port as we created and the port on the node is set to 30,008 because that's the port

that we specified in the definition file.

We can now use this port to access the web service using curl or a web browser.

So call 219216281.2, which is the IP of the node and then use the port 30,008 to access the web server.

So far, we talked about a service mapped to a single pod, but that's not the case all the time.

What do you do when you have multiple pods?

In the production environment, you have multiple instances of your web application running for high

availability and load balancing purposes.

In this case, we have multiple similar pods running our web application.

They all have the same labels with a key app and set to a value of my app.

The same label is used as a selector during the creation of the service.

So when the service is created, it looks for a matching pod with the label and finds three of them.

The service then automatically selects all the three parts as endpoints to forward the external request

coming from the user.

You don't have to do any additional configuration to make this happen.

And if you're wondering what algorithm it uses to balance the load across the three different parts,

it uses a random algorithm.

Thus the service acts as a built in load balancer to distribute load across different parts.

And finally, let's look at what happens when the parts are distributed across multiple nodes.

In this case, we have the web application on part on separate nodes in the cluster.

When we create a service without us having to do any additional configuration, Kubernetes automatically

creates a service that spans across all the nodes in the cluster and maps the target port to the same

node port on all the nodes in the cluster.

This way you can access your application using the IP of any node in the cluster and using the same

port number, which in this case is 30,008.

As you can see, using the IP of any of these nodes and I'm trying to curl to the same port and the

same port is made available on all the nodes part of the cluster.

To summarize in any case, whether it be a single port on a single node, multiple parts on a single

node or multiple pods on multiple nodes, the service is created exactly the same without you having

to do any additional steps during the service creation.

When pods are removed or added, the service is automatically updated, making it highly flexible and

adaptive.

Once created, you won't typically have to make any additional configuration changes.



