Porting projects to Qt5 and Python3
===================================

We have chosen to go for a compatible sources set, which can work with Qt4 or Qt5, Python2 or Python3.
Porting to Qt5 and to Python3 are two independent tasks.

Projects list
-------------

\[TODO]

How to setup a build workflow using Qt5 and/or python3
------------------------------------------------------

### Building

* Your system must have Qt5 / python3 + all modules installed. In casa-distro, Ubuntu-16.04 and ubuntu-18.04 develpment images are already ready for that.
* Edit the `bv_maker.cfg` file, and change in the `[ build ]` section:

    cmake_options = -DDESIRED_QT_VERSION=5 -DPYTHON_EXECUTABLE=/usr/bin/python3
    
* then run bv_maker to configure and build (either in casa-distro or directly depending on your config/setup)

### Running

To run programs using python3, either run explicitely python3:

    python3 <build_workflow>/bin/morphologist
    
But this is not very convenient, and many python scripts start with the standard shebang:

    #!/usr/bin/env python
    
which would normally call python2 because python2 is what is called via the `python` command on many systems up to now.
A simple fix for that is to make a symlink rom python3 to python in the `bin` directory of the build workflow:

    ln -s /usr/bin/python3 <builmd_workflow>/bin/python
    
Then once the build-workflow is setup (bv_env.sh has been sourced from this build workflow) and it is in the paths, the `python` command will switch to python3, and all python scripts will do the same.


Qt4 / Qt5
---------

Python2 / python3
-----------------

