---
layout: page
title: Guides and How-To's for OpenShift Assisted Installer
permalink: /guides/
---

# Guides and How-To's for OpenShift Assisted Installer

Welcome to our Guides and How-To's page for OpenShift Assisted Installer

## Available Guides and How-To's

Below is a list of Guides and How-To's for OpenShift Assisted Installer we've put together:

{% for post in site.guides %}
  - [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) 
{% endfor %}