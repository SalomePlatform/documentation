# Copyright (C) 2012-2013  CEA/DEN, EDF R&D, OPEN CASCADE
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public
# License along with this library; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307 USA
#
# See http://www.salome-platform.org/ or email : webmaster.salome@opencascade.com
#
CMAKE_MINIMUM_REQUIRED(VERSION 2.8.8 FATAL_ERROR)
INCLUDE(CMakeDependentOption)

PROJECT(SalomeDOCUMENTATION)

# Versioning
# ========
# Project name, upper case
STRING(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UC)

SET(${PROJECT_NAME_UC}_MAJOR_VERSION 7)
SET(${PROJECT_NAME_UC}_MINOR_VERSION 2)
SET(${PROJECT_NAME_UC}_PATCH_VERSION 0)
SET(${PROJECT_NAME_UC}_VERSION
  ${${PROJECT_NAME_UC}_MAJOR_VERSION}.${${PROJECT_NAME_UC}_MINOR_VERSION}.${${PROJECT_NAME_UC}_PATCH_VERSION})
SET(${PROJECT_NAME_UC}_VERSION_DEV 1)

# Our own set of macros:
# ==================
LIST(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/CMakeModules")
INCLUDE(SalomeMacros)


# Find prerequisites needed to documentation generation
# ==================================================
FIND_PACKAGE(SalomeDoxygen)
FIND_PACKAGE(SalomeGraphviz)
FIND_PACKAGE(SalomeSphinx)

# Directories
# =========
SET(SALOME_DOCUMENTATION_EXTRA_INSTALL extra CACHE PATH "Install path: SALOME Extra documentation")
SET(SALOME_DOCUMENTATION_GIT_INSTALL dev/git CACHE PATH "Install path: SALOME Git documentation")
SET(SALOME_DOCUMENTATION_CMAKE_INSTALL dev/cmake CACHE PATH "Install path: SALOME CMake documentation")
SET(SALOME_DOCUMENTATION_GUI_INSTALL gui CACHE PATH "Install path: SALOME GUI documentation")
SET(SALOME_DOCUMENTATION_TUI_INSTALL tui CACHE PATH "Install path: SALOME TUI documentation")

MARK_AS_ADVANCED(
 SALOME_DOCUMENTATION_EXTRA_INSTALL
 SALOME_DOCUMENTATION_GIT_INSTALL
 SALOME_DOCUMENTATION_CMAKE_INSTALL
 SALOME_DOCUMENTATION_GUI_INSTALL 
 SALOME_DOCUMENTATION_TUI_INSTALL
)

# Sources 
# =====
ADD_SUBDIRECTORY(tui)
ADD_SUBDIRECTORY(gui)
ADD_SUBDIRECTORY(extra)
ADD_SUBDIRECTORY(dev)

# Install rules
# ==========
FILE(GLOB COMMON_HTML_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.html")
FILE(GLOB COMMON_PNG_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.png")

INSTALL(FILES ${COMMON_HTML_FILES} ${COMMON_PNG_FILES} DESTINATION ${CMAKE_INSTALL_PREFIX})
