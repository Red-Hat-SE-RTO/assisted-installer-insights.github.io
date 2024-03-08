---
layout: default
title: Nutanix Installation Types for OpenShift Assisted Installer
permalink: /nutainx/
---

# Nutanix Installation Types for OpenShift Assisted Installer

Welcome to our Nutanix installation types page for OpenShift Assisted Installer. Here you can find instructions for deploying OpenShift on Nutanix platforms using the OpenShift Assisted Installer.

## Available Nutanix Installation Types

Below is a list of Nutanix installation types for OpenShift Assisted Installer we've put together:

{% for post in site.nutainx %}
  - [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) 
{% endfor %}