#Cloud Native Applications in a Private Cloud

In a large enterprise environment where most applications serve a small amount of users and rely heavily on large internal resources like databases and queuing systems, cloud native concepts can benefit an enterprise application in many different ways. A cloud native architecture can save resources, create more durable applications and provide many other user benefits.

##Hoards of Small Applications
###Not 2 application with 1000 servers each, 1000 applications with 2 servers each
Enterprise software developers can be responsible for hundreds of applications with small user bases that were traditionally hosted side by side on the same application and web servers. Shared run-times create many problems that include large blast radius, release/update downtime, log aggregation confusion, etc.

###Large central data (or other resource) requirement
Enterprise applications usually require interfacing with a large resource that is in turn interfaced with many other applications. These large resources sometimes contain sensitive data, firewalls, large data or CPU requirements that impede it's movement to the cloud. These resources are usually monolithic and cross many domains that also make it difficult for applications that interface with it to break off and take advantage of public cloud resources.

**Identify and abstract the application from these resources.**

* A middle-ground abstraction is to create API layers to these resources, preferably REST. 
    * This allows to keep ACID requirements where needed. Transactions, naturally, bind the application to external resource. If either caller or the callee fail a considerable amount of coupling is required to rollback.
    * A non-transactional *call and forget* will decouple the application from the resource, but there are very few ways to recover from failure.
* Using asynchronous communication will truly decouple from the resource. Fire and forget on a queuing system like AMQP allows for auto-retries, dead-letter queuing, fan-outs, and many other solutions for a communication breakdown between the sender and the receiver that would make Led Zeppelin proud.
* Eventual Consistency is good enough for a lot of use-cases. The question that needs to be asked is "Does the transaction need to be completely done to move on or can the application checked back when the data is needed?" If the application can have eventual consistency there is a cost and time savings.

##From Monolith to 12 factor
###Break off small pieces
Small pieces that are of the same domain or can handle an asynchronous coupling are good candidates to move to a cloud native architecture. Replacing a new piece of the application and tying back to the monolith as loosely coupled as possible will start to gain some of the advantages of cloud native architecture. 12 factor application architecture are also important guidelines to follow when rearchitecting monolith pieces. Stateless applications architecture and design one of the fundamental concepts that needs to be achieved. It is very important to remove any environment level state to the application.

###Containerization
Consider Docker containers for ease of deployment and concise application stack structure. Small containers can run on the same runtime to make use of larger servers and still be able to get the advantages of cloud native application architecture. Containers allow for operating system level separation that will make it possible to separate the application run time from the container runtime. Container management software like Kubernetes will allow for ease of deployment and management of the containers as well as cohesion between containers that need to interact.

##Disposable
One of the key concepts to cloud native architecture is that the application environment is disposable. Disposable environments promotes the practice of the application being removed and rejoining the ecosystem with very little impact.
###Cows vs Pets (Chickens vs Parrots)
Build and architect applications and environments to fail so that failure is expected and recoverable.
> "Everything fails all the time." --Warner Vogols

Design the application and environment to start up fast and when there is failure to be able to shutdown gracefully. There should be very little to nothing to recover when the environment fails. When an application and environment fail, instead of spending time to find the problem and fix the environment, throw away the environment and start up a new one. A tested and repeatable orchestration allows the application and it's environment to quickly recover. If a root cause analysis is needed, this can be done after the recovery from logging and event based monitoring. A lot of times when a troubled application stack is thrown away and rebuilt, the problem is solved (think of rebooting).

With proper continuous integration and continuous delivery the application environment should rebuild the whole environment when deploying an application to a testing environment. This accomplishes a couple of things:

1. Release process consistency. Every time there is a release of an application environment if happens the same way from development, to testing, to production.
2. Practice and testing of the disposing and rebuilding of an application environment.

##Reap the Benefits
###Recovering Testing and SDLC environments
With disposable environments, resources can be saved by only keeping environments up long enough to test, then throwing them away. Consider throwing away environments at no test times like overnight and weekends. Loosely coupled applications will be able to recover from other services not responding for a time. Create an environment that will exercise the cloud native concepts so that when it happens in production, it has happened many times before in testing. 

###A/B testing
With application environments smaller, more self contained and loosely coupled to other application environments, simplifies deploying two different versions for UX testing.

###Blue/Green deployments
Zero downtime releases where the new version of the application environment is deployed along side of the older version. The traffic can be bled off the older version to the newer version. Once all the traffic is bled off the old version can be thrown away, or kept around and off-line for analysis if needed. Blue/Green deployments will open up release windows since they do not impact the users, allowing continuous delivery to production. If all the tests have passed and there is no user impact, continuously deploy working versions.

##Auto-scaling
Horizontally scaling of the application environments up or down for the need of the users and the applications. A stateless cloud native application can easily add or subtract instances of itself to match the needs of the application.

###Stop wasting compute!
Build the application environment to have enough for normal load, failure and scale-up time. When needed, scale-up by increasing the amount of application environments to help out with the workload. The application environment can be scaled up on scheduled times, like prime time, or according to a system or application metric like CPU use percentage or queue backup. It is almost as important to scale down to take full advantage of the cloud native application environment and save resources.

###Make sure you are testing for auto-scaling
Test these failures and additions to your application environment regularly. Continuous deployment processes are good tests but also failure tests like Netflix's Chaos Monkey that randomly brings down services, blocks ports, etc. are good ways to harden your cloud naive application environment. Create automated testing that not only has good code coverage but also tests the entire environment stack. This will increase confidence to make changes, dispose of, and add application environments to the ecosystem of the enterprise. 

