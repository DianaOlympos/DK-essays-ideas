#State of cloud tooling

This is a dump of my ideas on the state of cloud tooling.

##Deploying to the cloud.

Setting: you go from scratch and need to put a "piece of code" live and running to serve some users.


###Type of actions
I see 3 different types of actions. They may overlap or the tooling may not be as separated, but that gives an idea.

1. *Provisioning*. This is about getting resources and attributing them to your account and payment services from the cloud providers. In that model, cloud provider offer common shared resources, and provisioning is the use of their API to isolate some of these resources for your use. Here we find Network, Tools-as-a-service, creation of profiles for access control to these resources, computing resources, storage, etc
2. *Configuration*. This mostly exist for computing resources. The goal here is to move the "raw" computing resources to a state of "specialized" resources that you can use without impediment for the next agents you want to deploy on top of it. This may be setting up the software that will handle the deployment, setup some security access/roles and associated certificates, setting up a default set of tools, etc.
3. *Deployment*. This is the act of bringing an artifact from the build process to all interested resources and recombine their access if needed, with the goal of deploying a new version of the software.

###
