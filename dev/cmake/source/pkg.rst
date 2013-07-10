Package detection mechanism
===========================

Philosophy and priority order
-----------------------------

The philosophy of the SALOME package detection is to rely as 
much as possible on the standard CMake modules.
It is assumed those modules will get better and better with newer releases of CMake
and doing so ensures future compatibility with newer versions of CMake.

The detection is however guided through a variable of the form XYZ_ROOT_DIR which
gives the root directory of an installation of the package. For example, to indicate
that we wish to use the Python installed in our home directory, one sets PYTHON_ROOT_DIR to
"/home/smith/Python-2.7.0".

The variable guiding the detection is always builts as::

  XYZ_ROOT_DIR

where <XYZ> is the upper case name of the standard CMake module. For example, the
detection of Qt4 is guided by setting QT4_ROOT_DIR.

The order of priority for the detection of a package is (from high to low priority):

1. CMake variables explicitly set by the user (typically on the command line with -DXYZ_ROOT_DIR=...)
2. Environment variables set by the user (with the same name XYZ_ROOT_DIR)
3. Default value based on a previous dependency using the tool already
4. Detection direclty in the host system by the standard CMake logic

The package detection is only made in the root CMakeLists.txt, potentially conditionned on some
user options. Package names are case-sensitive, but the corresponding variables are always upper-case.

Writing the detection macro of a new SALOME prerequisite
--------------------------------------------------------

All detection macros are located under the soure directory::

  salome_adm/cmake_files

or::

  adm_local/cmake_files

All prerequisite detection in SALOME should be implemented by:

* writing a file FindSalome<Xyz>.cmake (note the extra ''Salome''), where <Xyz> matches *exactly* the name of the standard CMake module (see below if there is no standard module for <Xyz>)
* typically this file looks like this::

    SALOME_FIND_PACKAGE_AND_DETECT_CONFLICTS(CppUnit CPPUNIT_INCLUDE_DIRS 1)
    MARK_AS_ADVANCED(CPPUNIT_INCLUDE_DIRS CPPUNIT_LIBRARIES CPPUNIT_CONFIG_BIN CPPUNIT_SUBLIB_cppunit CPPUNIT_SUBLIB_dl)

 
* It invokes the SALOME macro SALOME_FIND_PACKAGE_AND_DETECT_CONFLICTS() which takes:

  * as first argument the name of the package (here CppUnit), 
  * as second argument, the name of a (path) variable set when the package is found properly, 
  * and as third argument, the number of levels this variable should be browsed up to reach the root directory of the package installation.

* in the example above, we look for the package CppUnit (note that this is case-sensitive). There is already a standard CMake module to detect CppUnit, which sets the CMake variable CPPUNIT_INCLUDE_DIRS to the (list of) directories to include when compiling with CppUnit. Going one level up from the include directory (typically /usr/include) gives the root directory of the installation (/usr) 
* the reference variable may be a list, only its first element is then considered.
* all the variables exposed in the cache by the standard detection logic (CPPUNIT_INCLUDE_DIRS, CPPUNIT_LIBRARIES, etc ...) are marked as "advanced" so that they do not automatically appear in ccmake or cmake-gui.

Writing a new generic detection macro (advanced)
------------------------------------------------

If you need to include in SALOME a prerequisite for which the standard CMake distribution 
doesn't provide the FindXyz.cmake module, you will need to write it yourself.
This also applies if you judge that the standard FindXyz.cmake CMake module doesn't do its job
properly (yes, it can happen).

The following guidelines apply:

* make the module as generic as possible, considering that it should also run properly outside SALOME. This separates clearly the basic detection of the package from the SALOME logic. Basically the module represents the point 4. in the order of priority given above and should behave as much as possible like any standard CMake module
* invoking the FIND_LIBRARY(), FIND_PROGRAM(), FIND_PATH() and FIND_FILE() commands should be done without specifying an explicit PATH option to the command (this is not always possible - see for example FindOmniORBPy.cmake). The idea is that the root directory for the search is set by the SALOME encapsulation (by setting CMAKE_PREFIX_PATH)
* document properly which variables you are setting, respecting the CMake standard (see for example FindOmniORB.cmake)
* use the CMake code found in many standard modules::

    INCLUDE(FindPackageHandleStandardArgs)
    FIND_PACKAGE_HANDLE_STANDARD_ARGS(Graphviz REQUIRED_VARS GRAPHVIZ_EXECUTABLE)


* This macro takes care (among other things) of setting the XYZ_FOUND variable (upper case), and of displaying a message if not in QUIET mode (TBC).
* the macro should be saved in the same directory as above
* respect the naming conventions for the variables you set (start with the package name, upper case)
* here is a simple example of the detection of Sphinx::

    # - Sphinx detection
    #
    # Output variable: SPHINX_EXECUTABLE
    #                  
    # 
    # The executable 'sphinx-build' is looked for and returned in the above variable.
    #

    ###########################################################################
    # Copyright (C) 2007-2013  CEA/DEN, EDF R&D, OPEN CASCADE
    <...>
    ###########################################################################

    FIND_PROGRAM(SPHINX_EXECUTABLE sphinx-build)

    # Handle the standard arguments of the find_package() command:
    INCLUDE(FindPackageHandleStandardArgs)
    FIND_PACKAGE_HANDLE_STANDARD_ARGS(Sphinx REQUIRED_VARS SPHINX_EXECUTABLE)


Implementation details (advanced)
---------------------------------

The core of the SALOME detection logic is located in the macro
SALOME_FIND_PACKAGE_AND_DETECT_CONFLICTS() implemented in KERNEL/salome_adm/cmake_files/SalomeMacros.cmake.

The reader is invited to read the have the code at hand when reading the following.

The macro signature is
::

  SALOME_FIND_PACKAGE_DETECT_CONFLICTS(pkg referenceVariable upCount <component1> <component2> ...)

where:

* *pkg*              : name of the system package to be detected
* *referenceVariable*: variable containing a path that can be browsed up to retrieve the package root directory (xxx_ROOT_DIR)
* *upCount*          : number of times we have to go up from the path <referenceVariable> to obtain the package root directory.
* *<component_n>*    : an optional list of components to be found.  

For example::  

  SALOME_FIND_PACKAGE_DETECT_CONFLICTS(SWIG SWIG_EXECUTABLE 2) 

The macro has a significant size but is very linear:

1. Load a potential env variable XYZ_ROOT_DIR as a default choice for the cache entry XYZ_ROOT_DIR
   If empty, load a potential XYZ_ROOT_DIR_EXP as default value (path exposed by another package depending
   directly on XYZ)
2. Invoke FIND_PACKAGE() in this order:

    * in CONFIG mode first (if possible): priority is given to a potential "XYZ-config.cmake" file.
    * then switch to the standard MODULE mode, appending on CMAKE_PREFIX_PATH the above XYZ_ROOT_DIR variable.

3. Extract the path actually found into a temp variable _XYZ_TMP_DIR
4. Warn if XYZ_ROOT_DIR is set and doesn't match what was found (e.g. when CMake found the system installation
   instead of what is pointed to by XYZ_ROOT_DIR - happens when a typo in the content of XYZ_ROOT_DIR).
5. Conflict detection: check the temporary variable against a potentially existing XYZ_ROOT_DIR_EXP
6. Finally expose what was _actually_ found in XYZ_ROOT_DIR.  


The specific stuff (for example exposing a prerequisite of XYZ to the rest of the world for future conflict detection) is added after the call to the macro by the callee. See for example the FindSalomeHDF5.cmake macro which exposes the MPI_ROOT_DIR if HDF5 was compiled with parallel support.



