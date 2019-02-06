#State of cloud tooling

This is a dump of my ideas on the state of cloud tooling.

##Deploying to the cloud.

Setting: you go from scratch and need to put a "piece of code" live and running to serve some users.


###Type of actions
I see 3 different types of actions. They may overlap or the tooling may not be as separated, but that gives an idea.

1. *Provisioning*. This is about getting resources and attributing them to your account and payment services from the cloud providers. In that model, cloud provider offer common shared resources, and provisioning is the use of their API to isolate some of these resources for your use. Here we find Network, Tools-as-a-service, creation of profiles for access control to these resources, computing resources, storage, etc
2. *Configuration*. This mostly exist for computing resources. The goal here is to move the "raw" computing resources to a state of "specialized" resources that you can use without impediment for the next agents you want to deploy on top of it. This may be setting up the software that will handle the deployment, setup some security access/roles and associated certificates, setting up a default set of tools, etc.
3. *Deployment*. This is the act of bringing an artifact from the build process to all interested resources and recombine their access if needed, with the goal of deploying a new version of the software.

###Existing tooling

####Category 1: Provisioning
- Terraform
- CloudFormation
- Google Cloud equivalent
- Azure equivalent
- Cloud providers CLI tools
- Fugue
- Pulumi
- Cloud Provider web console
- Half implemented module for tools from category 2

####Category 2: Configuration
- Puppet
- Chef
- Ansible
- Salt
- Packer
- AMI
- Dockerfiles
- Nixops
- etc etc

####Category 3: deployment
- Kubernetes
- Swarm
- Dokku
- Habitat
- Disnyx
- Code Deploy
- GoCD
- Nomad

###Multi categories

There is an additional category: the tool that try to make a "one for all" that solve every type of actions. They tend to be useful in the sense that they provide a far easier to use API and can abstract a lot of the different interaction and complexity. They need far less knowledge to use to modify some of the settings of the full stack. They also tend to be highly opinionated and restrict your possibilities.

####Multi categories example
- Heroku
- Gigalixir
- AWS Elastic Beanstalk
- Google App Engine
- Azure Service Fabric
- etc

###Special case: the Cloud OS

I call Cloud OS the range of existing tools and projects that try to offer a different but stable set of service from any range of elastic resources. They tend to replicate existing "hardware" solution, but in a virtualised and abstracted ways. They use virtual machine images or containers to solve the level 2 problems. But they most of the time necessitate another set of tools and manipulation that reproduce level 1 action, but for their own virtual "resources". And they need additional tooling to handle level 3 actions.

In that sense, they do not solve any of the problem we talk here, but try to offer an agnostic but specialized to themselves set of abstractions, Services. If the "product" developer or ops know them, then they do not have to know any of the complexity of how things works "in the real world". This is basically a set of abstraction enabling to separate completely knowledge of the real world from the "product" team. (scaffolding on top of pile of shit, but someone else handle the shit).

In that sense, they act like an OS for the Cloud, hence the name.

####Cloud OS examples
- Kubernetes
- DC/OS
- OpenStack
- all the OpenStack derivatives
- Nomad (kind of)
- Manta

####Cloud OS Pros
There are multiple reasons why you would like to use one of these products.
1. There is no way you can bring your product developer to learn about the low level stack and the tooling associated with it. Or at least you need an intermediate step
2. You have decided your organization will separate the low level stuff from the product stuff. This is a reverse Conway's law
3. You have an "infrastructure" team and you need to find them a job. Moving everything to fully "fullstack" devs and product team would put these guy out of a job or force them to work with product. This is a too big organizational change than your company can handle
4. You are a cloud provider. In that case, this is basically what you need
5. You own real datacenters and want a private cloud. This is basically the same as 2. but at a larger scale with possibly a real financial reason
6. You are using the same abstraction as the rest of the industry, so it is fast to get up to speed new devs. This is kinda true due to k8s but at the same time not really
7. You have an existing set of toolings and stuff that are really awful to abstract the hardware and you need a step up.
8. You want to force your product team to stop considering their servers as pet.
9. For reason out of this scope (broken deployment and build tools), you need to use a container as the unit of deployment. In that case, you are building a cloud of containers on top of a cloud of VM.

####Cloud OS cons
1. They are just a way to avoid doing the real productive change: give full ownership to product teams
2. They simply do not work
3. They are really really complex
4. You are basically building a virtualised cloud on top of someone else cloud. Abstraction on top of abstractions. Urgh
5. You now need two different set of tools that do the same thing
6. You created new complex problems on top of the old one that you still have ... welcome
7. Your product team are learning the abstraction. They could have learned the real thing. Why ?
8. More complexity and abstraction make things far harder to debug
9. Your app/product code is now highly coupled to k8s and your tooling around it. You moved from pet server to pet app/product. It has not solved the problem that your product team build pet. Just move it one stack up
10. if you need containers that much, sitting down and rethinking your build and deployment strategy may help. See unicorn dream

##My point of view of deploying to the cloud: dreaming of unicorn

Multiple steps here. I will first describe my perfect world. Then point out where are things failing right now and what are the tooling that get close or can be used as inspiration.

###Dream stack
Shared need for all tools:
- allow collaboration across any number of people deploying and changing things concurrently. Trunk Based Development for infra/real CI/CD
- In case of error or incidents, reduce the cognitive load needed. Keep most of the thing running but take in information on what it need to let the human handle. Team-player
- Highly redundant
- Easy to learn and to interact with. Cue team-player. Allows on-boarding of people with no experience and few knowledge of infra and accompany them in their leveling up
- Can be modularized to allow on boarding of less knowledgeable users while still providing ability to dive in
- Observability, cue learning
- support auditing and role based access.
- Tools are open source, need for audit and patching if needed

####Level 1
This tool talk with all the cloud providers. It store the state it is handling and can show what is going to happen, when, in which order. What happened before. Navigate the full resources existing. Validate property on them (this URL can talk to this app deployed to these ports through the network, with Access policies, etc). The tool provide nice error message that are actionable. Can fail some things but keep doing work for the rest.
Declarative, driven by a git repo. Can handle multiple git repo and providers.

####Level 2
Nix with better discoverability of options and better modules to reuse out there more or less.

####Level 3
self organising app. Something kinda like Habitat. Apps are organised on a gossip network. Deployment is telling them which action to execute and letting them organise it themselves. They are self controling, see autopilot pattern. The unit of deployment/machine is self registering or draining from the other resources (load balancer, database, etc) and talk with other machines in the same app to choreography its deployments based on the action asked. Unit of deployment is a self contained self organising app with all needed sidecars.


###What is not there

####Level 1
Fugue is the closest but close source
Terraform is kinda there but the failure mode is abysmal and the language for declaration is newbie repellent
Cloud Provider specific tooling are arcane, broken, painful to use and newbie repellent
None of the tools support concurrent team based work.

####Level 2
Nix need documentation and discovery. More work is needed to show how it can be used "in the wild", in language that can be understood by product team. Need to show how to use it "ala docker compose" easily with something that make sense

####Level 3
Habitat is kinda there but has some problems. CodeDeploy show interesting case. The "gossip" idea may be nearly impossible to do for needs of HA deployment (how to make sure it is your turn to get out of the LB while keeping a minimum up ? While deploying fast enough ?)
Autopilot pattern is badly marketed and documented and hard to keep running
Dysnix need to be tested

##Gravitas

We offer to tackle the level 1 problem first. The reasons are multiple, but first it seems to be the one that are badly tackled by everyone and where it is the easiest to teach team. It will also be useable by more teams are needs are far less opinionated here. You just need cloud resources, there is no special concerns due to your own availability and all. Finally, Nix seems to kind solve level 2 already. And Level 3 is still an open space for exploration where ideas need to coalesce. It is also highly dependent of a good build process, and it seems most company and team out there are far from it and prefer to use containers.

The goal is to build a tool that can support
1. Concurrent work, TBD like, on the same cloud resources
2. Support all the major Cloud Providers
3. Do not force the human to handle everything in case of failure, but can adapt what to keep handling and what to give back dynamically as the situation evolve
4. Provide visibility constantly on what happens
5. Can keep the cloud in the state that is wanted
6. is driven through declarative state in git repo
7. Has validation and modularization at its core
8. is documented for every level of experience
9. As much as possible, over a path for moving in from terraform/cloudformation and a step to go back


I offer to build that by having a stateful service that keep checking the state of the cloud (see Fugue for an example of something close)
I offer to use Dhall as a language to declare the wanted state.

There is still research to do to decide if a declarative state is enough to drive the dynamicity we want
UX is open to discussion
Is distributed state and use it to ensure HA while keeping the state working with concurrent work.
Open research on how to handle shared resources between different "modules" (VPC...) and how to have "dynamic" modules (next /16 subnet in an existing VPC ? does it make sense ?)

###Elevator Pitch

We are building a cloud provisioning tool that work for all major cloud provider. It is a team player agent that always work to multiply the power of your team, even during incidents. It integrate in any team, adapts to their knowledge, enhance collaboration between teams.

Alternative
We are building a cloud provisioning tool with the same ease to work with than a member of your team. Think of it like an additional member of your team that will help you use the cloud and support you meaningfully even in the worst incidents.