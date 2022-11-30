---
title: Installation
description: >-
    Learn how to install the pipeline and its dependencies and how get started using PhyloNext for your analyses.
---

# Installation

PhyloNext was primarily developed for Linux. To run it from a command line on Windows, please use Windows Subsystem for Linux ([WSL](https://learn.microsoft.com/en-us/windows/wsl/install)).

All pipeline dependencies were encapsulated in containers (Docker and Singularity), which will be automatically downloaded with the first run of PhyloNext. Only the main dependencies must be installed manually. They include:
- Java
- Nextflow
- Container engine (either Docker or Singularity)

The following installation instruction is for Ubuntu.

## 1. Install [`Nextflow`](https://www.nextflow.io/docs/latest/getstarted.html#installation) (`>=22.10.0`)

Nextflow requires Java 11 (or later, up to 18) to be installed.

    sudo apt-get update
    sudo apt-get install default-jdk

Install Nextflow:

    wget -qO- https://get.nextflow.io | bash
    chmod +x ./nextflow
    mkdir -p ~/bin & mv ./nextflow ~/bin/


## 2. Install [`Docker`](https://docs.docker.com/engine/installation/)

For for details see [the official Docker documentation](https://docs.docker.com/engine/install/ubuntu/).

    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg lsb-release

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io

    sudo usermod -aG docker $USER
    newgrp docker


## 3. Download the pipeline and test it on a minimal dataset with a single command

    nextflow run vmikk/phylonext -r main -profile test,docker

