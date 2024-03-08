---
layout: default
title: Libvirt/KVM Installation Types for OpenShift Assisted Installer
permalink: /kvm/
---

# Libvirt/KVM Installation Types for OpenShift Assisted Installer

Welcome to our Libvirt/KVM installation types page for OpenShift Assisted Installer. Here you can find instructions for deploying OpenShift on Libvirt/KVM platforms using the OpenShift Assisted Installer.

## Available Libvirt/KVM Installation Types

Below is a list of Libvirt/KVM installation types for OpenShift Assisted Installer we've put together:

{% for post in site.kvm %}
  - [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) 
{% endfor %}