---
title: Troubleshooting
description: >-
    Common issues and solutions users might run into while using PhyloNext.
---

# Troubleshooting

Common issues and solutions users might run into while using PhyloNext.

!!! bug "Don't see your error/bug? Post an issue on GitHub"
    If you've encountered an error or bug not seen here, please post an issue at [PhyloNext GitHub Issues](https://github.com/vmikk/PhyloNext/issues). This will help greatly to track down the error and fix it!

## Pipeline errors

### Resume the workflow

!!! failure "Pipeline was interrupted"
    Any accidental failure - e.g., you closed the terminal, rebooted your computer, or the task was killed by a scheduler on HPC.

If the pipeline is interrupted, it can be resumed without having to start from scratch.  
Add the `-resume` flag to your command, and Nextflow will start the workflow from the last successfully executed process, 
retrieving all previous results from the cache.



### The number of available CPUs

!!! failure "Number of CPUs required"
    `Process requirement exceeds available CPUs -- req: 10; avail: 8`

Most likely, the number of CPUs configured for a process is larger than available on your system. 
To fix it, modify a config file and reduce the number of CPUs for workflow processes to fit your hardware. E.g., change
``` java
    withName:occ_filter{
        cpus = 10
    }
```
to 
``` java
    withName:occ_filter{
        cpus = 8
    }
```

See the `Configuration` section in the [usage documentation](https://phylonext.github.io/usage/#configuration-file).

### Pipeline revision version

!!! failure "Pipeline revision version"
    ```
    Project \`vmikk/phylonext\` is currently stickied on revision: main -- you need to explicitly specify a revision with the option \`-r\` in order to use it
    ```

Solution - specify a version number of the pipeline.  
To run the latest version, add `-r main` to your command.  
Alternatively, you may specify the exact version (or tag) you wish, e.g. `-r v.0.0.2`.  

## Installation problems

### Nextflow not found

!!! failure "Command not found"
    `command not found: nextflow`

If you installed Nextflow in the `~/bin` directory, most likely, it is not added to your PATH environment variable. 
To fix it, run the following command:

``` bash
export PATH="$HOME/bin:$PATH"
```
and try to execute Nextflow again.  

If it works, you may add this command to the `~/.bashrc` file to make these changes permanent. 
To do it from the command line, run:  

``` bash
echo "export PATH=$HOME/bin:$PATH" >> ~/.bashrc
```

!!! example "Notes on shells other than bash"
    If you are using a shell other than `bash`, please .  
    E.g., for `zsh` it should be `~/.zshrc`.  
    To know which shell are you using, run `echo $SHELL`


### Docker permissions

!!! failure "Docker - permission denied"
    `docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create?name=nxf-Cz6yqNjGI9AhRpsqkU4mSAHC": dial unix /var/run/docker.sock: connect: permission denied.`

Most likely, you forgot to add your user to the `docker` group and you need a super-user privileges to execute the container.  
To fix this error, run:  

``` bash
# Create the docker group.
sudo groupadd docker

#  Add your user to the docker group.
sudo usermod -aG docker ${USER}

# Log out and log back in so that your group membership is re-evaluated.
# Alternatively, to skip logging out, type the following command:  su -s ${USER}

# Verify that you can run docker commands without sudo
docker run hello-world
```

### Docker and WSL2

!!! failure "Docker and WSL2"
    `The command 'docker' could not be found in this WSL 2 distro. We recommend to activate the WSL integration in Docker Desktop settings.`

Try to enable `Use the WSL2 based engine` in the `Docker Desktop` settings.  

<a class="zoom" href="/assets/DokerDesktop_WSL2.png">
![WSL2 Docker Engine](/assets/DokerDesktop_WSL2.png)
</a>


### PhyloNext containers

If for some reason Nextflow is not able to download containers for PhyloNext, 
it's possible to pull them manually using:

``` bash
docker pull vmikk/rarrow:1.0.0
docker pull vmikk/biodiverse:1.0.0
docker pull vmikk/opentree:0.0.2
```
