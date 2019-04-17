# Drumknott (DK)

## Names
Beats ?


## Goal

This document search to define the goals and needs of a language used to control Gravitas.
In particular, the language will be used to describe cloud resources and the means to transform a current state of the world into the user's targeted one.
It should follow the Gravitas principles:
- *Observability*: The state and actions of Gravitas should be visible and easy to understand. Reasons for a decision and consequent action taken should be visible and *understandable* to a user.
- *Directability*: You need to be able to control and steer the direction Gravitas is going into, when you recognize a need to intervene. Gravitas should be flexible and able to take directions in the areas that need supervision, without the need for the user to take full control.  
- *Learnability*: Gravitas should be easy to pick up. We should also allow the user to grow their expertise alongside use of Gravitas. Gravitas handles a complex problem state and is itself a complex system. We consider that we should accompany our user from the first discovery of the system to being an expert of it.
- *Safety*: Gravitas needs to fulfill the highest standard for safety, security and ethics. We recognise our place in the ecosystem of software. As much as possible, we provide fail-safe situations and sane defaults. We aim to make the safe path easy. If a choice have to be made between resources and safety, Gravitas should balance its choice towards safety.
- *Stability*: As part of infrastructure, Gravitas' behaviour must be stable, and predictable. This means interfaces must be as stable as possible, upgrade paths must be easy and provided. Gravitas should allow services and workflow to be built on top of and around it.

## Needs

DK needs to be able to define at least two type of things.
- Targeted State: this is a stable static definition of what the targeted state of infrastructure is. It is the goal of the system to reach that state.
- Constraints: The user can define constraints on two things. The constraints can limits what is allowed in the final state. Or they can be used to define which actions are allowed to reach that state.

We also want DK to be able to communicate effectively to the user what to do and what happened if some of these constraints prove the final goal to be unattainable.

Additionally, we have some needs that comes from the domain we want to represent. We need to be able to differentiate between a *reference* to a piece of infrastructure that was described in the state from a data type that represent a scalar. There have been no decision yet on this. Two options are to not use reference except if the user explicitly generate one, or to have a type system that can differentiate these two data types automatically with inference, or if we run an unification/deduplication pass at some point.

## Type system
The addition of Constraints means that we need to execute a full DK program to be able to decide of its validity. As thus, we are looking at making DK a dynamic but strongly typed system.
The expected type so far are
- Constraints
- Integer
- String
- Multi-line String
- Decimal
- Map
- List
- Function
- Tuple/Array
- Reference
- Object

## Paradigms

DK is a rule based language in which functions and constraints are defined with pattern-matching.

## Constructs

- for-comprehension/map-reduce (see terminations rules)
- pattern-matching with structure extraction
- assignments
- import of modules, via web if needed

In general, the State part tries to be deterministic and termination proved.
The language in general is as side-effect free as possible. The only side-effect should be through backtracking in the constraints and possibly some headers and call for module imports.
