## How to Contribute

We welcome contributions to the Assisted Installer Insights repository! To contribute, follow these steps:

1. Fork this repository.
2. Create a new branch for your changes.
3. Make your changes and commit them to your branch.
4. Create a pull request against the `main` branch.

Please ensure that your contributions follow our [contributing guidelines](CONTRIBUTING.md) and [code of conduct](CODE_OF_CONDUCT.md).

### Sample Bare Metal Tutorial

To contribute a tutorial for Bare Metal installations, create a new markdown file in the `_baremetal` directory. For example, you can create a file named `2024-03-07-sample.markdown` with the following content:

```markdown
---
layout: default
title: Sample Bare Metal Tutorial
permalink: /bare-metal/sample-tutorial/
---

# Sample Bare Metal Tutorial

This is a sample tutorial for deploying OpenShift on Bare Metal using the Assisted Installer.

## Prerequisites

- A Bare Metal server with the following specifications:
  - 2 CPUs
  - 4GB RAM
  - 60GB disk space

## Steps

1. Install the Assisted Installer on the Bare Metal server.
2. ...

## Conclusion

This tutorial demonstrated how to deploy OpenShift on Bare Metal using the Assisted Installer.
```

This tutorial will be available at `/bare-metal/sample-tutorial/` after it is merged into the `main` branch.
