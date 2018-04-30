=============
Release Notes
=============

This is the list of changes to scikit-build between each release. For full
details, see the commit logs at http://github.com/scikit-build/scikit-build

Next Release
============

New Features
------------

* CMake module :doc:`\cmake-modules/PythonExtensions`: Set symbol visibility to export only the module init function.
  This applies to GNU and MSVC compilers. Thanks :user:`xoviat`. See :issue:`299`.

* Add CMake module :doc:`\cmake-modules/F2PY` useful to find the ``f2py`` executable for building Python
  extensions with Fortran. Thanks to :user:`xoviat` for moving forward with the integration. Concept for the
  module comes from the work of :user:`scopatz` done in `PyNE <https://github.com/pyne/pyne>`_ project.
  See :issue:`273`.

* Update CMake module :doc:`\cmake-modules/NumPy` setting variables ``NumPy_CONV_TEMPLATE_EXECUTABLE``
  and ``NumPy_FROM_TEMPLATE_EXECUTABLE``. Thanks :user:`xoviat` for the contribution. See :issue:`278`.

* Use ``_skbuild/platform-X.Y`` instead of ``_skbuild`` to build package. This allows to have a different build
  directory for each python version. Thanks :user:`isuruf` for the suggestion and :user:`xoviat` for contributing
  the feature. See :issue:`283`.

* Run cmake and ``develop`` command when command ``test`` is executed.

* Add support for :ref:`cmake_languages <usage-cmake_languages>` setup keyword argument.

Bug fixes
---------

* Fix support of ``--hide-listing`` when building wheel.

* CMake module :doc:`\cmake-modules/Cython`: Fix escaping of spaces associated with ``CYTHON_FLAGS`` when
  provided as command line arguments to the cython executable through CMake cache entries. See :issue:`265`
  fixed by :user:`neok-m4700`.

* Ensure package data files specified in the ``setup()`` function using ``package_data`` keyword are packaged
  and installed.

* Support specifying a default directory for all packages not already associated with one using syntax like
  ``package_dir={'':'src'}`` in ``setup.py``. Thanks :user:`benjaminjack` for reporting the issue.
  See :issue:`274`.

* Improve ``--skip-cmake`` command line option support so that it can re-generate a source distribution or a python
  wheel without having to run cmake executable to re-configure and build. Thanks to :user:`jonwoodring` for reporting
  the issue on the `mailing list <https://groups.google.com/forum/?utm_medium=email&utm_source=footer#!topic/scikit-build/-ManO0dhIV4>`_.

* Set ``skbuild <version>`` as wheel generator.
  See `PEP-0427 <https://www.python.org/dev/peps/pep-0427/#file-contents>`_ and :issue:`191`.

Python Support
--------------

* Tests using Python 3.3.x were removed and support for this version of python is not guaranteed anymore. Support was
  removed following the deprecation warnings reported by version 0.31.0 of wheel package, these were causing the tests
  ``test_source_distribution`` and ``test_wheel`` to fail.

Tests
-----

* Speedup execution of tests that do not require any CMake language enabled. This is achieved by (1) introducing the
  test project ``hello-no-language``, (2) updating test utility functions ``execute_setup_py`` and ``project_setup_py_test``
  to accept the optional parameter ``disable_languages_test`` allowing to skip unneeded compiler detection in test project
  used to verify that the selected CMake generator works as expected, and (3) updating relevant tests to use the new test
  project and parameters.

  Overall testing time on all continuous integration services was reduced:

  * AppVeyor:

    * from **~16 to ~7** minutes for 64 and 32-bit Python 2.7 tests done using Visual Studio Express 2008
    * from more than **2 hours to ~50 minutes** for 64 and 32-bit Python 3.5 tests done using Visual Studio 2015. Improvement specific
      to Python 3.x were obtained by caching the results of slow calls to ``distutils.msvc9compiler.query_vcvarsall`` (for Python 3.3 and 3.4) and
      ``distutils._msvccompiler._get_vc_env`` (for Python 3.5 and above).
      These functions were called multiple times to create the list of :class:`skbuild.platform_specifics.windows.CMakeVisualStudioCommandLineGenerator`
      used in :class:`skbuild.platform_specifics.windows.WindowsPlatform`.


  * CircleCI: from **~7 to ~5** minutes.

  * TravisCI: from **~21 to ~10** minutes.

* Update maximum line length specified in flake8 settings from 80 to 120 characters.

* Add ``prepend_sys_path`` utility function.

* Ensure that the project directory is prepended to ``sys.path`` when executing test building sample project
  with the help of ``execute_setup_py`` function.

* Add codecov config file for better defaults and prevent associated Pull Request checks from reporting failure
  when coverage only slightly changes.

Documentation
-------------

* Improve internal API documentation:

  * :mod:`skbuild.platform_specifics.windows`
  * :mod:`skbuild.command`
  * :mod:`skbuild.command.generate_source_manifest`
  * :mod:`skbuild.utils`

Cleanups
--------

* Fix miscellaneous pylint warnings.

Scikit-build 0.6.1
==================

Bug fixes
---------

* Ensure CMake arguments passed to scikit-build and starting with ``-DCMAKE_*``
  are passed to the test project allowing to determine which generator to use.
  For example, this ensures that arguments like ``-DCMAKE_MAKE_PROGRAM:FILEPATH=/path/to/program``
  are passed. See :issue:`256`.

Documentation
-------------

* Update :doc:`/make_a_release` section including instructions to update ``README.rst``
  with up-to-date pypi download statistics based on Google big table.


Scikit-build 0.6.0
==================

New features
------------

* Improve ``py_modules`` support: Python modules generated by CMake are now
  properly included in binary distribution.

* Improve developer mode support for ``py_modules`` generated by CMake.


Bug fixes
---------

* Do not implicitly install python modules when the beginning of their name
  match a package explicitly listed. For example, if a project has a package
  ``foo/__init__.py`` and a module ``fooConfig.py``, and only package ``foo``
  was listed in ``setup.py``, ``fooConfig.py`` is not installed anymore.

* CMake module :doc:`\cmake-modules/targetLinkLibrariesWithDynamicLookup`: Fix the
  caching of *dynamic lookup* variables. See :issue:`240` fixed by :user:`blowekamp`.

Requirements
------------

* wheel:  As suggested by :user:`thewtex`, unpinning version of the package
  by requiring ``>=0.29.0`` instead of ``==0.29.0`` will avoid uninstalling a newer
  version of wheel package on up-to-date system.

Documentation
-------------

* Add a command line :ref:`CMake Options <usage_cmake_options>` section to :doc:`Usage <\usage>`.

* Fix :ref:`table <Visual Studio>` listing *Visual Studio IDE* version and
  corresponding with *CPython version* in :doc:`/generators`.

* Improve :doc:`/make_a_release` section.

Tests
-----

* Extend ``test_hello``, ``test_setup``, and ``test_sdist_hide_listing`` to
  (1) check if python modules are packaged into source and wheel distributions
  and (2) check if python modules are copied into the source tree when developer
  mode is enabled.

Internal API
------------

* Fix :meth:`skbuild.setuptools_wrap.strip_package` to handle empty package.

* Teach :meth:`skbuild.command.build_py.build_py.find_modules` function to look
  for `py_module` file in ``CMAKE_INSTALL_DIR``.

* Teach :class:`skbuild.utils.PythonModuleFinder` to search for `python module`
  in the CMake install tree.

* Update :meth:`skbuild.setuptools_wrap._consolidate` to copy file into the CMake
  tree only if it exists.

* Update :meth:`skbuild.setuptools_wrap._copy_file` to create directory only if
  there is one associated with the destination file.

Scikit-build 0.5.1
==================

Bug fixes
---------

* Ensure file copied in "develop" mode have "mode bits" maintained.


Scikit-build 0.5.0
==================

New features
------------

* Improve user experience by running CMake only if needed. See :issue:`207`

* Add support for :ref:`cmake_with_sdist <usage-cmake_with_sdist>` setup keyword argument.

* Add support for ``--force-cmake`` and ``--skip-cmake`` global :ref:`setup command-line options <usage-setuptools_options>`.

* scikit-build conda-forge recipe added by :user:`isuruf`.
  See `conda-forge/staged-recipes#1989 <https://github.com/conda-forge/staged-recipes/pull/1989>`_

* Add support for `development mode <https://packaging.python.org/distributing/#working-in-development-mode>`_. (:issue:`187`).

* Improved :doc:`/generators` selection:

 * If available, uses :ref:`Ninja` build system generator on all platforms. An
   advantages is that ninja automatically parallelizes the build based on the number
   of CPUs.

 * Automatically set the expected `Visual Studio` environment when
   ``Ninja`` or ``NMake Makefiles`` generators are used.

 * Support `Microsoft Visual C++ Compiler for Python 2.7 <http://aka.ms/vcpython27>`_.
   See :issue:`216`.

* Prompt for user to install the required compiler if it is not available. See :issue:`27`.

* Improve :doc:`/cmake-modules/targetLinkLibrariesWithDynamicLookup`  CMake Module extending
  the API of ``check_dynamic_lookup`` function:

 * Update long signature: ``<LinkFlagsVar>`` is now optional
 * Add support for short signature: ``check_dynamic_lookup(<ResultVar>)``.
   See `SimpleITK/SimpleITK#80 <https://github.com/SimpleITK/SimpleITK/pull/80#issuecomment-267617180>`_.

Bug fixes
---------

* Fix scikit-build source distribution and add test. See :issue:`214`
  Thanks :user:`isuruf` for reporting the issue.

* Support building extension within a virtualenv on windows. See :issue:`119`.

Documentation
-------------

* add :doc:`/generators` section

* add :doc:`/changes` section

* allow github issues and users to easily be referenced using ``:issue:`XY```
  and ``:user:`username``` markups.
  This functionality is enabled by the `sphinx-issue <https://github.com/sloria/sphinx-issues>`_ sphinx extension

* make_a_release: Ensure uploaded distributions are signed

* usage:

 * Add empty cross-compilation / wheels building sections
 * Add :ref:`Why should I use scikit-build ? <why>`
 * Add :ref:`Setup options <usage-setup_options>` section

* hacking:

 * Add :ref:`internal_api` section generated using `sphinx-apidoc`.

 * Add :ref:`internal_cmake_modules` to document :doc:`/cmake-modules/targetLinkLibrariesWithDynamicLookup`
   CMake module.

Requirements
------------

* setuptools: As suggested by :user:`mivade` in :issue:`212`, remove the
  hard requirement for ``==28.8.0`` and require version ``>= 28.0.0``. This allows
  to "play" nicely with conda where it is problematic to update the version
  of setuptools. See `pypa/pip#2751 <https://github.com/pypa/pip/issues/2751>`_
  and `ContinuumIO/anaconda-issues#542 <https://github.com/ContinuumIO/anaconda-issues/issues/542>`_.

Tests
-----

* Improve "push_dir" tests to not rely on build directory name.
  Thanks :user:`isuruf` for reporting the issue.

* travis/install_pyenv: Improve MacOSX build time updating `scikit-ci-addons`_

* Add ``get_cmakecache_variables`` utility function.

.. _scikit-ci-addons: http://scikit-ci-addons.readthedocs.io

* Add ``get_cmakecache_variables`` utility function.

Internal API
------------

* :meth:`skbuild.cmaker.CMaker.configure`: Change parameter name from ``generator_id``
  to ``generator_name``. This is consistent with how generator are identified
  in `CMake documentation <https://cmake.org/cmake/help/v3.7/manual/cmake-generators.7.html>`_.
  This change breaks backward compatibility.

* :meth:`skbuild.platform_specifics.abstract.CMakePlatform.get_best_generator`: Change parameter name
  from ``generator`` to ``generator_name``. Note that this function is also directly importable
  from :mod:`skbuild.platform_specifics`.
  This change breaks backward compatibility.

* :class:`skbuild.platform_specifics.abstract.CMakeGenerator`: This class allows to
  handle generators as sophisticated object instead of simple string. This is done
  anticipating the support for `CMAKE_GENERATOR_PLATFORM <https://cmake.org/cmake/help/v3.7/variable/CMAKE_GENERATOR_PLATFORM.html>`_
  and `CMAKE_GENERATOR_TOOLSET <https://cmake.org/cmake/help/v3.7/variable/CMAKE_GENERATOR_TOOLSET.html>`_. Note also that the
  class is directly importable from :mod:`skbuild.platform_specifics` and is now returned
  by :meth:`skbuild.platform_specifics.get_best_generator`. This change breaks backward compatibility.


Cleanups
--------

* appveyor.yml:

 * Remove unused "on_failure: event logging" and "notifications: GitHubPullRequest"
 * Remove unused SKIP env variable


Scikit-build 0.4.0
==================

New features
------------

* Add support for ``--hide-listing`` option

 * allow to build distributions without displaying files being included

 * useful when building large project on Continuous Integration service limiting
   the amount of log produced by the build

* CMake module: ``skbuild/resources/cmake/FindPythonExtensions.cmake``

 * Function ``python_extension_module``: add support for `module suffix <https://github.com/scikit-build/scikit-build/commit/0a9b7ef>`_

Bug fixes
---------

* Do not package python modules under "purelib" dir in non-pure wheel

* CMake module: ``skbuild/resources/cmake/targetLinkLibrariesWithDynamicLookup.cmake``:

 * Fix the logic checking for cross-compilation (the regression
   was introduced by :issue:`51` and :issue:`47`

 * It configure the text project setting `CMAKE_ENABLE_EXPORTS <https://cmake.org/cmake/help/v3.6/prop_tgt/ENABLE_EXPORTS.html?highlight=enable_export>`_ to ON. Doing
   so ensure the executable compiled in the test exports symbols (if supported
   by the underlying platform)

Docs
----

* Add `short note <http://scikit-build.readthedocs.io/en/latest/cmake-modules.html>`_
  explaining how to include scikit-build CMake module
* Move "Controlling CMake using scikit-build" into a "hacking" section
* Add initial version of `"extension_build_system" documentation <http://scikit-build.readthedocs.io/en/latest/extension_build_system.html>`_

Tests
-----

* tests/samples: Simplify project removing unneeded install rules and file copy

* Simplify continuous integration

 * use `scikit-ci <http://scikit-ci.readthedocs.io/en/latest/>`_ and
   `scikit-ci-addons`_
 * speed up build setting up caching

* Makefile:

 * Fix `coverage` target
 * Add `docs-only` target allowing to regenerate the Sphinx documentation
   without opening a new page in the browser.

Scikit-build 0.3.0
==================

New features
------------

* Improve support for "pure", "CMake" and "hybrid" python package

 * a "pure" package is a python package that have all files living
   in the project source tree

 * an "hybrid" package is a python package that have some files living
   in the project source tree and some files installed by CMake

 * a "CMake" package is a python package that is fully generated and
   installed by CMake without any of his files existing in the source
   tree

* Add support for source distribution. See :issue:`84`

* Add support for setup arguments specific to scikit-build:

 * ``cmake_args``: additional option passed to CMake
 * ``cmake_install_dir``: relative directory where the CMake project being
   built should be installed
 * ``cmake_source_dir``: location of the CMake project

* Add CMake module ``FindNumPy.cmake``

* Automatically set ``package_dir`` to reasonable defaults

* Support building project without CMakeLists.txt



Bug fixes
---------

* Fix dispatch of arguments to setuptools, CMake and build tool. See :issue:`118`

* Force binary wheel generation. See :issue:`106`

* Fix support for ``py_modules`` (`6716723 <https://github.com/scikit-build/scikit-build/commit/6716723>`_)

* Do not raise error if calling "clean" command twice

Documentation
-------------

* Improvement of documentation published
  on http://scikit-build.readthedocs.io/en/latest/

* Add docstrings for most of the modules, classes and functions

Tests
-----

* Ensure each test run in a dedicated temporary directory

* Add tests to raise coverage from 70% to 91%

* Refactor CI testing infrastructure introducing CI drivers written in python
  for AppVeyor, CircleCI and TravisCI

* Switch from ``nose`` to ``py.test``

* Relocate sample projects into a dedicated
  home: https://github.com/scikit-build/scikit-build-sample-projects

Cleanups
--------

* Refactor commands introducing ``set_build_base_mixin`` and ``new_style``

* Remove unused code