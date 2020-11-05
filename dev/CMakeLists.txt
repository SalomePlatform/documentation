# Copyright (C) 2012-2021  CEA/DEN, EDF R&D, OPEN CASCADE
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
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

# Install content from subdirs
# ============================

ADD_SUBDIRECTORY(git)
ADD_SUBDIRECTORY(cmake)

# Install [dev] documentation for SALOME modules
# ==============================================

SET(_modules
  FIELDS
  PARAVIS
)

FOREACH(_module ${_modules})
  SET(${_module}_ROOT_DIR $ENV{${_module}_ROOT_DIR})
  IF(${_module}_ROOT_DIR)
    SALOME_INSTALL_MODULE_DOC(${${_module}_ROOT_DIR}/share/doc/salome/dev/${_module}
                              DESTINATION ${SALOME_INSTALL_DOC_DEV})
  ENDIF()
ENDFOREACH()

# Special processing for MEDCoupling
# ==================================

SET(MEDCOUPLING_ROOT_DIR $ENV{MEDCOUPLING_ROOT_DIR})
IF(MEDCOUPLING_ROOT_DIR)
  SALOME_INSTALL_MODULE_DOC(${MEDCOUPLING_ROOT_DIR}/share/doc/developer
                            DESTINATION ${SALOME_INSTALL_DOC_DEV}/MEDCoupling)
  SALOME_INSTALL_MODULE_DOC(${MEDCOUPLING_ROOT_DIR}/share/doc/tutorial
                            DESTINATION ${SALOME_INSTALL_DOC_DEV}/MEDCoupling)
  SALOME_INSTALL_MODULE_DOC(${MEDCOUPLING_ROOT_DIR}/share/doc/user
                            DESTINATION ${SALOME_INSTALL_DOC_DEV}/MEDCoupling
                            INDEX html)
ENDIF()
