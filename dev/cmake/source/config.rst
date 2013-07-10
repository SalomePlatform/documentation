Exposing a module's configuration (advanced)
============================================

(to be completed)

Most important SALOME modules (KERNEL, GUI, MED) expose their configuration in a dedicated CMake file.

Following the CMake tutorial (http://www.cmake.org/Wiki/CMake/Tutorials/How_to_create_a_ProjectConfig.cmake_file) some 
important module install \*Config.cmake files facilitating the configuration of other dependent modules. When writing such 
CMake files, care must be taken to use PROJECT_BINARY_DIR and PROJECT_SOURCE_DIR variables instead of CMAKE_BINARY_DIR and CMAKE_SOURCE_DIR. This ensures that the project will also work properly when included as a sub-folder of a bigger project.

The use of the local (or global) CMake Registry (via the EXPORT(PACKAGE ...) command) is not recommended. This overrides 
some of the FIND_PACKAGE mechanisms and quickly becomes a mess when compiling several versions of the same package on the 
same machine (typically the case for SALOME developers).

Only level one dependencies configuration are exported in the XXXConfig.cmake files (direct dependencies).

The rules are a bit lengthy but very generic. The idea is to define a set of targets to be exported, and to then explicitly
call the export command to generate the appropriate files.

With this setup, the project can be either:

* configured, built and installed in a standalone fashion
* or directly be included as a sub-folder in the code of another bigger project. A simple ADD_SUBDIRECTORY in the root CMakeLists.txt of the encapsulating project will trigger the proper configuration/build of the sub-project.

Care must be taken to explicitly request a target to be part of the export set in the code sub-folders, when installing it
(EXPORT option)::

  INSTALL(TARGETS kerncompo kern_main
     # IMPORTANT: Add the library kerncompo to the "export-set" so it will be available 
     # to dependent projects
     EXPORT ${PROJECT_NAME}Targets
     LIBRARY DESTINATION "${INSTALL_LIB_DIR}"
     RUNTIME DESTINATION "${INSTALL_BIN_DIR}")



possibility to have a minimal specification on the prerequisites (technically one just need to set the _ROOT_DIR variables for the packages that were never found before), and to get "for free" what was already found in a dependent module
