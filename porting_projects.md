Porting projects to Qt5 and Python3
===================================

We have chosen to go for a compatible sources set, which can work with Qt4 or Qt5, Python2 or Python3.
Porting to Qt5 and to Python3 are two independent tasks.

Projects list
-------------

- baby
- Brainrat
- casa-distro
- CatiDB
- Constellation
- cortical_surface (must be tested)
- data_storage_client
- DISCO
- freesurfer (must be tested)
- longitudinal
- Neuroimaging toolbox (nit)
- QualiCATI
- Sacha
- Snapbase (must be tested)
- SPM
- Whasa

How to setup a build workflow using Qt5 and/or python3
------------------------------------------------------

### Building

* Your system must have Qt5 / python3 + all modules installed. In casa-distro, Ubuntu-16.04 and ubuntu-18.04 development images are already ready for that.
* Edit the `bv_maker.cfg` file, and change in the `[ build ]` section:

        cmake_options = -DDESIRED_QT_VERSION=5 -DPYTHON_EXECUTABLE=/usr/bin/python3
    
* then run bv_maker to configure and build (either in casa-distro or directly depending on your config/setup)

### Running

To run programs using python3, either run explicitly python3:

    python3 <build_workflow>/bin/morphologist
    
But this is not very convenient, and many python scripts start with the standard shebang:

    #!/usr/bin/env python
    
which would normally call python2 because python2 is what is called via the `python` command on many systems up to now.
A simple fix for that is to make a symlink from python3 to python in the `bin` directory of the build workflow:

    ln -s /usr/bin/python3 <builmd_workflow>/bin/python
    
Then once the build-workflow is setup (bv_env.sh has been sourced from this build workflow) and it is in the paths, the `python` command will switch to python3, and all python scripts will do the same.


Qt4 / Qt5
---------

### imports:

Don't import directly PyQt5 or PyQt4. We have a compatibility module: [soma.qt_gui.qt_backend](http://brainvisa.info/soma-base/sphinx/api.html#module-soma.qt_gui.qt_backend) which replaces the explicit imports:

        from soma.qt_gui.qt_backend import Qt
        widget = Qt.QWidget()
        # etc.

The `qt_backend` module supports PyQt4, PyQt5 and PySide transparently, and offers a few functions to help links with [matplotlib](https://matplotlib.org/) (see [init_matplotlib_backend](http://brainvisa.info/soma-base/sphinx/api.html#soma.qt_gui.qt_backend.init_matplotlib_backend)) or for the few functions which do not have the same API in the different implementations (like [QFileDialog.getOpenFileName](http://brainvisa.info/soma-base/sphinx/api.html#soma.qt_gui.qt_backend.getOpenFileName)).
If you need to explicitly initialize the backend to a specific Qt implementation, do it **only** in main scripts directly called from the user, not in modules/libraries which may be imported.

        from soma.qt_gui import qt_backend
        qt_backend.set_qt_backend('PyQt5')
        from soma.qt_gui.qt_backend import Qt
        widget = Qt.QWidget()
        # etc.

But the best way is *not* to force this, and rely on the [`QT_API`](https://stackoverflow.com/questions/52930781/qt-api-environment-variable) environment variable that any user can set:

        export QT_API=pyqt5
        soma_workflow_gui # will use PyQt5.
        
The `QT_API` variable is already set by the `bv_env` program according to the settings of the build workflow, so you even don't have to care about it in `bv_maker`and `casa-distro`.

### major changes:

* No very deep changes in the API, most code written for Qt4 will work with Qt5.
* One of the most important points however is in **Pyqt4/PyQt5 handling of slots**: in a slot function, when an exception is raised, in PyQt4 the slot would end silently and the program would go on its normal life, but in PyQt5, an exception would cause the whole program to end with an error. So **check / catch exceptions in callbacks**
* The **QtWebKit module has been deprecated**, and removed in Ubuntu 18/Qt4. This needs to explicitly switch between QtWebKit (Qt4) and QtWebEngine (Qt5) instead (with a `if` in codes).
* A layout problem seems to exist in QGLWidget with Qt5, which causes sometimes GL widgets to appear outside of their parent widget (in a non-reproducible way). We're working on replacing it with QOpenGLWidget in Qt5 in Anatomist.


Python2 / python3
-----------------

[official porting doc](https://docs.python.org/3/howto/pyporting.html)

Things to check:

* use ``from __future__ import print_function`` at the beginning of source files
* ``print "babar"`` -> ``print("babar")``
* ``print >> file, "babar"`` -> ``print("babar", file=file)``
* ``print "babar", `` -> ``print("babar", end='')``
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
* ``execfile()`` -> ``with open(file[, encoding='utf-8']) as f: six.exec_(f.read()[, ...])``, or use ``soma.importer.execfile()``
* ``raise (exc_type, exc_instance, exc_traceback)`` -> ``six.reraise(exc_type, exc_instance, exc_traceback)``
* ``try: .. ; except Exception, e:`` -> ``try: ...; except Exception as e:``
* iterators: ``iterator.next()`` -> ``next(iterator)``
* to get the 1st item of a generator: ``next(iter(generator))``
* comparison / sorting operators: redefine ``something.__hash__()`` when needed, 
* ``__cmp__`` is deprecated and not called anymore, sometimes needs to redefine ``__lt__``, ``__gt__``, ``__eq__`` etc.
* ``map(func, list)`` -> ``[func(x) for x in list]``, or ``(func(x) for x in list)`` if a generator is OK
* ``reduce(func, list)`` -> ``six.moves.reduce(func, list)``
* ``filter(func, list)`` -> ``[x for x in list if func(x)]``, or ``(x for x in list if func(x))`` if a generator is OK
* `0777` -> `0o777`: `0777` is a syntax error in python3
* dict ordering / iteration order is different and non-reproducible in python 3.0-3.6
* The real nightmare is strings: str, unicode, bytes, and encoding...
* Encoding also causes problems to open text files.
* Some functions used to return `str` and now return `bytes`, for instance ``file.read()`` when no encoding is used, ``sqlite3`` requests results...
* sharing pickled objects between python 2 and 3 is almost impossible, mainly because of these str/unicode/bytes/encoding problems
* int divisions: in python2:

                >>> 3 / 2
                1

  in python3:

                >>> 3 / 2
                1.5

  thus, use ``3 // 2`` when possible.
* in python2, there are two types `int` and `long`: `2L`. In python3, all ints are longs, and `long` does not exist any longer. `2L` is a syntax error.
