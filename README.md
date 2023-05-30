# itom project #

welcome to the open source software **itom**. It allows operating measurement systems with multiple hardware components, like cameras, AD-converters, actuators, motor stages as well as handling your lab automation. The graphical user interface provides a quick and easy access to all components, complex measurement tasks and algorithms can be scripted using the embedded python scripting language and self-defined user interfaces finally provide a possibility to adapt **itom** to your special needs. External hardware or algorithms are added to **itom** by an integrated plugin system.

In order to learn more about **itom**, see the official homepage [itom.bitbucket.io](http://itom.bitbucket.io) or read the [user documentation](http://itom.bitbucket.io/latest/docs/)

### What is this repository for? ###

* This repository is the main access to the itom core **itom** repository and it's affiliated side repositories **designerplugins** and **plugins**.


### How do I get set up? ###

* Clone this repositoriy and initialize the submodules and update them:
    ```
    git clone --recursive git@github.com:itom-project/itomProject.git
    cd itomproject
    git submodule foreach --recursive git checkout master
    ´´´

For more specific information how to build itom, please take a look at the **itom** core submodule.


### Contribution ###

You are welcome to use and test **itom**. If you want to you are invited to participate in the development of **itom** or some of its plugins. If you found any bug, feel free to post an issue.

### Contact ###

**itom** is being developed since 2011 by

> [Institut für Technische Optik](http://www.uni-stuttgart.de/ito)

> University of Stuttgart

> Stuttgart

> Germany

in co-operation with 
> [twip Optical Solutions GmbH](http://www.twip-os.com)

> Stuttgart

> Germany
