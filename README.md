
# Cloud Native Applications in a Private Cloud

In large enterprise environments, most applications serve a small amount of users and rely heavily on large internal resources. A cloud native architecture can save resources, create more durable applications, and provide other user benefits.

## Hordes of Small Applications

### 1000 applications with 2 servers per
Enterprise software developers are often responsible for hundreds of applications with small user bases. Traditionally, these applications were hosted on a server, side by side. Shared run-times between applications often create problems including large blast radius, release/update downtime, and log aggregation confusion... to name a few. In addition to resource sharing, enterprise servers may contain sensitive data and firewalls which impede migration and increase traffic to the cloud. Also, server resources may be monolithic and cross several domains, adding another hurdle for applications needing to break off and take advantage of public cloud resources.

**Identify and abstract the application from these resources.**

* Middle-ground abstraction: create API layers to these resources (databases, webservices, ESBs), preferably REST.
	* This allows to keep ACID requirements where needed. Transactions, naturally, bind the application to external resource. Warning, if either caller or the callee fail, a considerable amount of coupling is required to rollback.
    * A non-transactional *call and forget* can decouple the application from the resource. Warning: there are very few ways to recover from a failure.
* True decoupling: asynchronous communication will truly decouple the application from the enterprise resource. Fire and forget on a queuing system like AMQP allows for auto-retries, dead-letter queuing, fan-outs, and many other solutions for a communication breakdown between the sender and the receiver.

Eventual Consistency suffices for many of use-cases. The question, "Does the transaction need to be completely done to move on or can the application check back when the data is needed?" If the application can have eventual consistency, there is cost and time savings with an eventual consistency approach.

## From Monolith to 12 factor
### Break off small pieces
Small pieces from the same domain or those which can handle an asynchronous coupling, are good candidates to move to a cloud native architecture. Replacing a new piece of the application and tying back to the monolith, coupled as loosely as possible, the application begins to gain some of the advantages of cloud native architecture. [12 factor](https://12factor.net/) application architecture are important guidelines to follow when rearchitecting monoliths. Stateless application design and architecture are the fundamental 12 factor concepts that needs to be achieved. It is important to remove any environment level state to the application.

### Containerization
Consider Docker containers for ease of deployment and concise application stack structure. Containers allow for operating system level modularization that make it possible to separate the application run time from the container runtime. Container management software like Kubernetes allows for ease of deployment and management of the containers as well as cohesion between containers that depend on each other.

## Disposable
One of the key concepts to cloud native architecture is that the application environment is disposable. Disposable environments promote the practice of the application entering and exiting the ecosystem with little impact.
### Cows vs Pets (Chickens vs Parrots)
> "Everything fails all the time." --Warner Vogols

Build and architect your applications and environments with an eye to failure, so that when failure occurs, it is expected and recoverable. Design the application environment to shutdown gracefully and start up fast. A tested and repeatable orchestration will allow the application, and its environment, to quickly recover. When an application and environment fail, instead of spending time to find the problem and fix the environment, it can be beneficial to throw away the environment and start up a new one. Often when a troubled application stack is thrown away and rebuilt, the problem will be solved (think of rebooting). If a root cause analysis is needed after a failure, this can be done after recovery from logging and event based monitoring.

With proper continuous integration and continuous delivery the application environment should rebuild the whole environment when deploying an application to a testing environment. This accomplishes a couple of things:

1. Release process consistency. Every time there is a release of an application environment it happens the same way from development, to testing, to production.
2. Practice makes perfect. Practice the disposing, rebuilding and testing of an application environment.

## Reap the Benefits
### Recovering Testing and SDLC environments
With disposable environments, resources are conserved by only keeping environments up long enough to test, then deleting them. Consider throwing away environments at off test times such as overnight or on weekends. Loosely coupled applications can recover from other services not responding for a time. Create an environment that will exercise these cloud native concepts, so that when it happens in production, it has happened before in testing.

### Ease of multiple deployments
Smaller application environments that are self contained and loosely coupled to other application environments, simplify the deployment of two different versions of the same environment. This could be used for the elusive A/B testing of UX prototypes.
Also, zero downtime releases, where the new version of the application is deployed with the older version, accomplish blue/green deployments. The traffic is then bled off the older version to the newer version. Once all the traffic is bled off, the old version can be thrown away or kept around and offline for analysis if needed. Blue/Green deployments will open up release windows since they do not impact the users, allowing continuous delivery to production. If all the tests have passed and there is no user impact, continuously deploy working versions.

## Auto-scaling
Horizontally scaling of the application environments up or down for the need of the users and the applications. A stateless cloud native application can easily add or subtract instances of itself to match the needs of the application. When the need of CPU, memory or traffic load increases, the ability to horizontally scale with more application environments decreases cost and downtime, as opposed to putting the application in a bigger machine and vertically scaling.

### Stop wasting compute!
Build the application environment to have enough for normal load, failure and scale-up time. When needed, scale-up by increasing the amount of application environments to help out with the workload. The application environment can be scaled up on scheduled times, like prime time, or according to a system or application metric like CPU use percentage or queue backup. It is just as important to scale down to take full advantage of the cloud native application environment and reduce cost.

### Make sure you are testing for auto-scaling
Test the scaling of instances in your application environment regularly. Continuous deployment processes are good, but failure tests like Netflix's Chaos Monkey which randomly brings down services, blocks ports, etc. are also good ways to harden your cloud naive application environment. Create automated testing that not only has good code coverage but also tests the entire environment stack. This will increase confidence to make changes, dispose of, and add application environments to the ecosystem, and the enterprise.
