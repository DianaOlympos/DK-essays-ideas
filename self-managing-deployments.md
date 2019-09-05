# Self-managing deployment tool

## Problem

- small team (5 to 10)
- SMB up to 100 devs in dozen of teams
- Limited resources
- team is self sufficient
- cloud deployments
- secrets ?
- one service per VM (no need for complex orchestration)
- Autoscaling handled out of band
- Blue/Green too slow: Deployment need to be under 2 min

How to deploy an artifact built in CI ?

## Existing solutions

- Chef/Puppet : need master and not made for this
- Habitat: Nearly there, but need a central "master" for storing artefacts
- Nomad/K8s: need master and/or cluster management
- Ansible: not declarative, handled by master or user laptop, need SSH access, inventory is a pain
- AWS CodeDeploy: Master but managed. Lock-in without equivalents (non lockin version based on ansible ?)

For all of these, how to handle secrets ?

## Proposed solution

- Supervisor on every machine
- Use the service system of the machine (systemd ?) or possibly do it yourself (plugin ?)
- Generate a gossip cluster (see Partisan)
- Can do rolling-deploy (select a leader ?) and rollback in case of problems (maybe ?) (what if leader dies ? partition during deployments ?)
- Have hooks/events for important lifecycle moments that start scripts
- use package manager of the machine or installed at least (nix/apt/dnf/yum/...)
- Deps management is through package manager
- Take artefacts from any S3 compliant and ssh/scp
- Can get a message through the gossip to deploy new version

Still not fixed : secrets ? env var and env var file generated through a lifecycle hook is easy. Do we want more integration ?

## Business model

- By default only use event hooks that the user have to supply
- accept plugins that handle most of the "normal" packages (nix, debs, rpm, with systemd, etc)
- If you want to get it all in "one package" kept up to date : pay for the distro or do your own
- Free distro for "non production" usage (evaluation and non commercial entity ?)
- Trademark to avoid other distro to use your name.
- Pay for support and help in building your config
- Red Hat Model.
