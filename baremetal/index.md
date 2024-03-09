---
layout: page
title: Bare Metal Installation Types for OpenShift Assisted Installer
permalink: /baremetal/
---

# Bare Metal Installation Types for OpenShift Assisted Installer

Welcome to our Bare Metal installation types page for OpenShift Assisted Installer. Here you can find instructions for deploying OpenShift on Bare Metal platforms using the OpenShift Assisted Installer.

## Available Bare Metal Installation Types

Below is a list of Bare Metal installation types for OpenShift Assisted Installer we've put together:

{% for post in site.baremetal %}
  - [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) 
{% endfor %}