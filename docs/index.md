# Overview

This is a workshop to learn Kubernetes with the target to use it on a home/remote server setup.

## Prerequisits

To walk through this workshop the following knowledge and local setup is recommended or required:

* Linux, MacOs or (not preferred) Windows laptop with 8GB, better 16GB of memory.
* Basic computer knowledge with interacting with the command line interface.
* Basic knowledge with Docker containers is recommended, but not needed.

## The chapters

The workshop start easy with the setup of a local environment and explaining basics from the ground up. Experienced users may already proceed to the "explore" chapter or work on the challenge sections. Please give feedback showing whether the content is "too heavy" or "too lightweight". Please also ask at any time in case something is not clear.

The local setup will be used to demonstrate basic concepts. The local setup will then be used to show tools that are required or helpful in a home server setup. The last chapter shows how to deploy Kubernetes on a home or remote server. In case you have a remote server available, nice you can try it. If not, the concept is shown enabling you to verify whether a Kubernetes home setup is suitable for your needs.

The workshop tries to balance new users with experienced users and is planned for a 3 hour session. The progress is flexible, but the following milestones are rough estimation:

* **Minute 000 - 030** - Local setup
* **Minute 030 - 090** - Chapter Kubernetes Basics
* **Minute 090 - 150** - Chapter Explore
* **Minute 150 - 180** - Chapter Homeserver

### [Local setup](./setup.md)

A local development environment will be set up. This environment is to be used for local development for learning and exploring. But it may also be used to try out a few of the recommendations mentioned in the chapter "homeserver" in case no target server is available during the workshop.

### [Kubernetes Basics](./basics.md)

Within this chapter a very short intro to Kubernetes will be given. The focus is on topics that are really necessary to know, leaving the details for later. Bridging the gap for users that are used to work with Linux, but not with Kubernetes.

### [Explore](./explore/overview.md)

This chapter will showcase a few projects that are easy to install, but would be very complex to be installed without Kubernetes superpowers. Parts of the examples are especially useful for the home setup and will be used in the homeserver setup.

### [Homeserver](./homeserver/overview.md)

This chapter focuses on the idea of using Kubernetes for homeserver setups. Knowledge from a few years "running in production" will be given. A tool called ["kubespray"](https://github.com/kubernetes-sigs/kubespray) will be introduced. Kubespray is a collection of Ansible playbooks enabling you to set up your home server.

In addition various other projects essential for a cluster are shown.
