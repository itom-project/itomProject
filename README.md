# itom project #

welcome to the open source software **itom**. It allows operating measurement systems with multiple hardware components, like cameras, AD-converters, actuators, motor stages as well as handling your lab automation. The graphical user interface provides a quick and easy access to all components, complex measurement tasks and algorithms can be scripted using the embedded python scripting language and self-defined user interfaces finally provide a possibility to adapt **itom** to your special needs. External hardware or algorithms are added to **itom** by an integrated plugin system.

In order to learn more about **itom**, see the official homepage [itom-project.github.io](https://itom-project.github.io/) or read the [user documentation](http://itom-project.github.io/latest/docs/)

### What is this repository for? ###

* This repository is the main access to the itom core **itom** repository and it's affiliated side repositories **designerplugins** and **plugins**.

### How do I get set up? ###
* Clone this repositoriy and initialize the submodules and checkout the latest master them:
```bash
    git clone --recursive --remote git@github.com:itom-project/itomProject.git
    cd itomProject
    git submodule foreach --recursive git checkout master
```
* To Clone a specific Tag please enter:
```bash
    git clone --recursive --remote --branch <tag_name> git@github.com:itom-project/itomProject.git
```


For more specific information how to build itom, please take a look at the **itom** core submodule.

## Contribution
You are welcome to use and test [``itom``](https://itom-project.github.io/ "``itom``"). If you want to you are invited to participate in the development of [``itom``](https://itom-project.github.io/ "``itom``") or some of its plugins. If you found any bug, feel free to post an [issue](https://github.com/itom-project/itom/issues "issue").

### pre-commit hooks
After the first cloning of the repositories, the [pre-commit](https://pre-commit.com/ "pre-commit") hooks should be installed once.
```bash
pip install pre-commit
```
#### (optional) run against all files
It's usually a good idea to run the hooks against all of the files when adding new hooks (usually ``pre-commit`` will only run on the changed files during git hooks).
Tehrefore got to the main folder of the respective itom repository (e.g. itomProject, itom, plugins or designerplugins), which includes the ``pre-commit`` configuration
file **.pre-commit-config.yaml**. Then run:

```bash
pre_commit run --all-files
```

# Licensing
The core components and the main application of itom are covered by the [GNU Library General Public Licence (GNU LGPL)](https://github.com/itom-project/itom/blob/master/COPYING.txt "GNU Library General Public Licence (GNU LGPL)"). All components belonging to the SDK of [``itom``](https://itom-project.github.io/ "``itom``") (e.g. dataObject, pointCloud, addInInterface,…) are additionally covered by an [``itom``](https://itom-project.github.io/ "``itom``") exception. The main idea of this exception is to allow other libraries (e.g. plugins) to include and link agains components of itom SDK independent on the specific license model of the respective "other" library. All files belonging to the itom SDK are included in the folder SDK that is shipped with any setup or included in the build directory (when build from sources).

## itom Exception
The full text license of LGPL and itom [exception](https://github.com/itom-project/itom/blob/master/LGPL_EXCEPTION.txt "exception") is also included as file [COPYING](https://github.com/itom-project/itom/blob/master/COPYING.txt "COPYING") in the source distributions and setups.

All plugins and designer-plugins that can be integrated into itom can have their own licensing. Therefore the user is referred to the specific licensing documents or statements of each external library (plugin).

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
