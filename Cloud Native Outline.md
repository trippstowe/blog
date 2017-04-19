#Cloud Native Applications in a Private Cloud

##Small Application with internal data requirements
Not 2 application with 1000 servers each, 1000 applications with 2 servers each
###Large central data (or other resource) requirement
* Internal use only
* Find a way to abstract
    * API layers
        * ACID
            Transactional but it will bind your application to external resource
        * Call and forget
            Call and log error if cannot complete and move on. Cannot usually recover lost call.
    * Asynchronous communication
        * Eventual Consistency 
            Can you do this? If, so it saves a lot of time and money
            Fire and Forget

##From Monolith to 12 factor
###Break off small pieces
* Find pieces of the monolith that can be abstracted
    * Different domains are a good candidate
* Rewrite and replace
* Loosely coupled!
    * Avoid connecting directly back to monolith
    * Queue or at worst REST
* Get rid of environment level state

###Containerization
* Consider containerizing for ease of deployment
* Small pieces put together so changes have less of an impact
    * Small blast radius
    * Kubernetes to have lots of small pieces working together

##Disposable
###Cows vs Pets (Chickens vs Parrots)
* Build applications and environments to fail
    * "Everything fails all the time." --Warner Vogols
    * Start up fast and shutdown gracefully
* When something fails throw it all away and rebuild
* Throw away for deployments
* Practice throwing away and rebuilding

###Testing and SDLC environments
* Only keep environment up long enough to test, then throw away
* Consider throwing away environments at no test time

###A/B testing
* Two different versions for UX testing
* Disposable environments make this a lot easier

###Blue/Green deployments
* Release structure where there are two different versions 
* Traffic is bled off to the new versions as they come up
* Old version are thrown away as they are bled off
* No user downtime
    * Can open up other release windows (like right after testing is done, whenever that is)

##Auto-scaling
###Stop wasting compute!
* Have enough for normal run and failure
* Scale up when needed
    * Scheduled times vs Unknown times
* Don't forget to scale down!

###Make sure you are testing for auto-scaling
* Test these failures and additions to your pool
* Code Coverage!
