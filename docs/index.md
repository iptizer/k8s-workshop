# Overview

This is a workshop to learn Kubernetes with the target to use it on a home/remote server setup.

## The chapters

The workshop start easy with the setup of a local environment and explaining basics from the ground up. Experienced users may already proceed to the "explore" chapter. Please give feedback showing whether the content is "too heavy" or "too lightweight".

### [Setup of a development environment](./setup/overview.md)

A local development environment will be set up. This environment is to be used for local development for learning and exploring. But it may also be used to try out a few of the recommendations mentioned in the chapter "homeserver" in case no target server is available during the workshop.

### [Kubernetes Basics](./basics/overview.md)

Within this chapter a very short intro to Kubernetes will be given. The focus is on topics that are really necessary to know, leaving the details for later. Bridging the gap for users that are used to work with Linux, but not with Kubernetes.

### [Explore](./explore/overview.md)

This chapter will showcase a few projects that are easy to install, but would be very complex to be installed without Kubernetes superpowers.

### [Homeserver](./homeserver/overview.md)

This chapter focuses on the idea of using Kubernetes for homeserver setups. Knowledge from a few years "running in production" will be given. A tool called ["kubespray"](https://github.com/kubernetes-sigs/kubespray) will be introduced. Kubespray is a collection of Ansible playbooks enabling you to set up your home server.

In addition various other projects essential for a cluster are shown.
