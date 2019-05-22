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

### imports:

Don't import directly PyQt5 or PyQt4. We have a compatibility module: [soma.qt_gui.qt_backend](http://brainvisa.info/soma-base/sphinx/api.html#module-soma.qt_gui.qt_backend) which replaces the explicit imports:

        from soma.qt_gui.qt_backend import Qt
        widget = Qt.QWidget()
        # etc.
        
If you need to explicitly initialize the backend to a specific Qt implementation, do it **only** in main scripts directly called from the user, not in modules/libraries which may be imported.

        from soma.qt_gui import qt_backend
        qt_backend.set_qt_backend('PyQt5')
        from soma.qt_gui.qt_backend import Qt
        widget = Qt.QWidget()
        # etc.

But the bast way is *not* to force this, and rely on the [`QT_API`](https://stackoverflow.com/questions/52930781/qt-api-environment-variable) environment variable that any user can set:

        export QT_API=pyqt5
        soma_workflow_gui # will use PyQt5.

### major changes:

* No very deep changes in the API, most code written for Qt4 will work with Qt5.
* The QtWebKit module has been deprecated, and removed in Ubuntu 18/Qt4. This needs to switch between QtWebKLit (Qt4) and QtWebEngine (Qt5) instead.
* A layout problem seems to exist in QGLWidget with Qt5, which causes sometimes GL widgets to appear outside of their parent widget (in a non-reproducible way). We're working on replacing it with QOpenGLWidget in Qt5 in Anatomist.


Python2 / python3
-----------------

https://docs.python.org/3/howto/pyporting.html

Things to check:

* use ``from __future__ import print_function`` at the beginning of source files
* ``print "babar"`` -> ``print("babar")``
* use [six](https://pythonhosted.org/six/)
* ``dict.iteritems()`` -> ``six.iteritems(dict)``
* ``dict.iterkeys()`` -> ``six.iterkeys(dict)``
* ``dict.itervalues()`` -> ``six.itervalues(dict)``
* check what is made from results of ``dict.keys()``, ``dict.values()``, ``dict.items()``: they were lists in pyton2, they are generators (iterators) in python3. Sometimes needs to wrap them into real lists: ``list(dict.values())`` is a list (but there is a double list, copied in python2, hence an overhead).
* imports are absolute: relative imports should be prefixed with `.`:

            import .submodule
            from .submodule import babar
  
  also possible to use (for python2):
            
            from __future__ import absolute_import
            
* `unicode` does not exist, `str` is actually unicode. Sometimes just defining `if sys.version_info[0] >= 3: unicode=str` is enough, sometimes not...
* metaclasses are different: use ``six.with_metaclass()``
* ``apply(func, list)`` -> ``func(*list)``
* some modules have changed name/position in python3. `six.moves` may help:
* ``import cPickle`` -> ``from six.moves import cPickle`` (or use the regular ``import pickle``)
* ``import StringIO`` -> ``from six.moves import StringIO``
* ``types.StringTypes`` -> ``six.string_types``
* ``urllib2.urlopen()`` -> ``six.moves.urllib.request.urlopen()``
* ``execfile()`` -> ``six.exec_()``
* dict ordering / iteration order is different and non-reproducible in python 3.0-3.6
* The real nightmare is strings: str, unicode, bytes, and encoding...
* Encoding also causes problems to open text files.
* Some functions used to return `str` and now return `bytes`, for instance ``file.read()`` when no encoding is used, ``sqlite3`` requests results...
* sharing pickled objects between python 2 and 3 is almost impossible, mainly because of these str/unicode/bytes/encoding problems
