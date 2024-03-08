---
layout: default
title: VMware Installation Types for OpenShift Assisted Installer
permalink: /vmware/
---

# VMware Installation Types for OpenShift Assisted Installer

Welcome to our VMware installation types page for OpenShift Assisted Installer. Here you can find instructions for deploying OpenShift on VMware platforms using the OpenShift Assisted Installer.

## Available VMware Installation Types

Below is a list of VMware installation types for OpenShift Assisted Installer we've put together:

{% for post in site.vmware %}
  - [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) 
{% endfor %}