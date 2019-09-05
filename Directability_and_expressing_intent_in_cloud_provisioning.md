#Expressing intent and directability of cloud provisioning agents

The problem of cloud provisioning is usually thought of as a two sided automation problem. You can use a tool based on declarative state, or you can use an imperative like tool. This control an agent that will work on your behalf with the cloud provider of resources to generate the infrastructure you desired. This dichotomy is mostly linked to mutable vs immutable infrastructure.

We will try first to explain shortly these two ways to handle cloud provisioning. We will show how they break in term of making the tool they use a team-player agent. We will then offer a third approach, that seems to be offered by the industry but not widely accepted as best practice. This approach will be based on intent expression.

##The declarative state/imperative actions dichotomy

Cloud Provisioning today is separated in two worlds. The Declarative State world use tools like CloudFormation, Terraform, Pulumi, NixOps and equivalents. The Imperative Actions world use tools like aws-cli, scripts, some plugins for Ansible, Puppet or Chef. All this code is used to define what is expected from an "agent" that will handle provisioning the resources. In the Imperative world, this is an executor that has access to the cloud resources and will follow the instructions. In the Declarative world, it is an agent that can scan the existing resources, compare that to the blueprint provided and fill the voids.

Things differ in the instructions provided to the agent. The Imperative world provide a list of actions to apply. This can be linearly applied or be a more complex network, but the agent is still expected to be a simple executor. In that sense, it is automation in the most basic expression. We took a list of action and build a robot to apply them always the same. The agent can have some capabilities to decided if an action is needed or not (does that file already exist ?) but in general, it is just an off-load of human decision.

The Declarative world give a lot more agency to the agent. The instructions given are only the definition of the final state desired. A blueprint. Up to the agent to check what is the current state of the resources and to decided how to go from current state to desired state. Sometimes a human will check the plan before letting the agent execute it, but in general, the agent decide of its actions.

While in the Imperative case, the agent does not do enough decisions to be considered part of a team, a Declarative agent is in my opinion autonomous enough to be consider an independent member of the team of operators that maintain the Cloud resources system. But is it then a team player ?

##Cloud Provisioning tool as a non team-player

You can refer to [https://ieeexplore.ieee.org/document/1363742](Ten challenges for making automation a "team player" in joint human-agent activity) (make a blog post about it?) to understand the concept of automation as a team player. For our purpose here, we will consider that agents are a team player if they follow the following rules:
    * Enter into an agreement, which we call a Basic Compact, that the participants intend to work together
    * Be mutually predictable in their actions
    * Be mutually directable
    * Maintain common ground

In the Declarative world, usually what is declared is only the intended end state. There is no definition or control over the steps the agent will take to reach that goal. If you want some specific state to happen in the middle, you have to do the work yourself. In that sense, these agents are not *directable*.

An simplified example. You want to move your website from the domain "softwaremaxims.com" to "software-maxims.com". To do that, you will need to do two actions. Redirect the old DNS record to the new domain and add the new one. So you can declare that you now want the new record to point to your load balancer instead of the old one. And the old to redirect to that record. Easy enough, two lines change. You apply the change and let the agent decide the actions to take.

Are you sure that the redirect will only be created *after* the new record is created ? Well no. You have no way to specify that. You can probably infer it will happen in that way, and i hope your agent is intelligent enough to do it that way. At least in this simple case. But it is a really oversimplified case. What you would really want to be able to do its to keep your Declarative case (because you do want to give that agency to the tool, it enable a lot of autonomous decisions) and to be able to direct the behavior of the agent. You would like to be able to "direct" your agent. To give him some directions to follow and to help him understand what needs to be done.

##Automation as a team player

So if you want to be able to control that behaviour, you want to teach your agent what it should do or not do. One way to do that is to define some rules. You would like to be able to state Rules, Policies, Predicate, Validations... Call them however you want. What you want is to be able to tell the system "this is what i want, respecting these constraints". And then the agent will decide on the actions to take based on the knowledge it has.

Now can you tell me which of these Cloud Provisioning tools give you a way to define these constraints ?

Hashicorp introduced their language "Sentinel", but it is not integrated by default in Terraform, works outside of the usual framework of Terraform and can not express easily all these needs. Do you think it would make sense to get that kind of control over an agent ?


