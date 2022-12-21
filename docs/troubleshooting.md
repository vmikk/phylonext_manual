---
title: Troubleshooting
description: >-
    Common issues and solutions users might run into while using PhyloNext.
---

Common issues and solutions users might run into while using PhyloNext.

!!! error "Don't see your error/bug? Post an issue on GitHub"
    If you've encountered an error or bug not seen here, please post an issue at [PhyloNext GitHub Issues](https://github.com/vmikk/PhyloNext/issues). This will help greatly to track down the error and fix it!


## The number of available CPUs

!!! error "Number of CPUs required"
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

See the `Configuration` section in the [usage documentation](usage.md).
