---
title: Web GUI
description: >-
    Web-based graphical user interface for PhyloNext.
---

To facilitate easy and efficient navigation for exploring the PhyloNext pipeline, 
a user-friendly, web-based graphical user interface (GUI) has been developed 
by Thomas Stjernegaard Jeppesen (https://github.com/thomasstjerne).  

The GUI is available at [**https://phylonext.gbif.org/**](https://phylonext.gbif.org/).  

!!! warning "GBIF user account required"
    To access the GUI, users must have a GBIF user account.  
    To register an account, please visit [https://www.gbif.org/](https://www.gbif.org/).

This interface offers a simplified approach to accessing phylogenetic diversity information, 
requiring minimal technical expertise. Built as a single-page application 
using the React.js JavaScript framework, the GUI effectively controls 
the PhyloNext pipeline by communicating with a REST web service written in Node.js.  

**WebGUI - configuration screen**  
![WebGUI App - Configuration](/assets/WebGUI_App.webp){ loading=lazy }  


**WebGUI - run progress**  
![WebGUI App - Progress](/assets/WebGUI_Progress.webp){ loading=lazy }  


**WebGUI - results screen**  
![WebGUI App - Results](/assets/WebGUI_Results.webp){ loading=lazy }  



The source code for the GUI can be found at 
[https://github.com/gbif/phylonext-ui/](https://github.com/gbif/phylonext-ui/), 
while the web service code is available at 
[https://github.com/gbif/phylonext-ws](https://github.com/gbif/phylonext-ws).  


!!! bug "Issues with the GUI"
    If you encounter any errors, please kindly report them on the GitHub issue tracker:   
    [https://github.com/gbif/phylonext-ui/issues](https://github.com/gbif/phylonext-ui/issues)  

