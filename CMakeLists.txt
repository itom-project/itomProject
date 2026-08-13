# - itomproject software -
# URL: http://www.uni-stuttgart.de/ito
# Copyright (C) 2022, Institut fuer Technische Optik (ITO),
# Universitaet Stuttgart, Germany
#
# itomproject is free software; you can redistribute it and/or modify it
# under the terms of the GNU Library General Public Licence as published by
# the Free Software Foundation; either version 2 of the Licence, or (at
# your option) any later version.
#
# itomproject is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU Library
# General Public Licence for more details.
#
# You should have received a copy of the GNU Library General Public License
# along with multipoint. If not, see <http://www.gnu.org/licenses/>.


cmake_minimum_required(VERSION 3.12...3.29)

message(
    STATUS "------------------- PROJECT ItomProject ---------------------")

# Retrieve Version Number and Identifier from GIT-TAG
include(itom/cmake/VersionFromGit.cmake)
version_from_git(
  LOG       ON
  TIMESTAMP "%Y%m%d%H%M%S"
)

# define cmake project and version number
project(itomproject VERSION ${GIT_VERSION})

message(
    STATUS "------------------- Version = ${itomproject_VERSION} -----------------------\n")
if(BUILD_QTVERSION STREQUAL "Qt6")
    set(CMAKE_CXX_STANDARD 17)
elseif(BUILD_QTVERSION STREQUAL "Qt5")
    set(CMAKE_CXX_STANDARD 11)
endif(BUILD_QTVERSION STREQUAL "Qt6")

set(CMAKE_CXX_STANDARD_REQUIRED ON)

message(STATUS "CMAKE_CXX_STANDARD: ${CMAKE_CXX_STANDARD}")

# Enable solution folders in the Visual Studio generator and move the
# Qt AUTOMOC/AUTOUIC/AUTORCC helper targets (<Target>_autogen and
# <Target>_autogen_timestamp_deps) into a dedicated folder so they no longer
# clutter the top level of the solution.
# These global properties are supported across CMake 3.12 up to 4.x.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property(GLOBAL PROPERTY AUTOGEN_TARGETS_FOLDER "autogen")
set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMakePredefinedTargets")

option(ENABLE_ITOM_EXTERNAL "Enable the the itom external dependencies." OFF)
option(BUILD_ITOM_EXTERNAL "Build with the itom external dependencies." OFF)


list(APPEND CMAKE_MODULE_PATH
    "${CMAKE_CURRENT_LIST_DIR}/cmake"
    "${CMAKE_CURRENT_LIST_DIR}/external"
    "${CMAKE_CURRENT_LIST_DIR}/external/cmake"
    "${CMAKE_CURRENT_LIST_DIR}/external/cmake/modules"
    "${CMAKE_SOURCE_DIR}/cmake/modules/")

if(ENABLE_ITOM_EXTERNAL)
    if(BUILD_ITOM_EXTERNAL)
        include(external_setup)
        if(NOT EXTERNAL_FLAG_BUILD_COMPLETE)
            return()
        endif(NOT EXTERNAL_FLAG_BUILD_COMPLETE)
    else(BUILD_ITOM_EXTERNAL)
        include(external_versions)
        include(external_components)
        include(external_find_packages)
    endif(BUILD_ITOM_EXTERNAL)
endif(ENABLE_ITOM_EXTERNAL)

option(BUILD_ITOM_CORE "Build the itom core library and executables." ON)
if(BUILD_ITOM_CORE )
    set(CMAKE_FOLDER "itom")
    add_subdirectory(itom)
endif(BUILD_ITOM_CORE )

option(BUILD_ITOM_DESIGNERPLUGINS "Build the itom Designerplugins." ON)
if(BUILD_ITOM_DESIGNERPLUGINS )
    set(CMAKE_FOLDER "designerPlugins")
    add_subdirectory(designerplugins)
endif(BUILD_ITOM_DESIGNERPLUGINS)

option(BUILD_ITOM_PLUGINS "Build the itom plugins." ON)
if(BUILD_ITOM_PLUGINS )
    set(CMAKE_FOLDER "plugins")
    add_subdirectory(plugins)
endif(BUILD_ITOM_PLUGINS)

option(BUILD_ITOM_PRIVATEPLUGINS "Build the itom private plugins." OFF)
if(BUILD_ITOM_PRIVATEPLUGINS )
    set(CMAKE_FOLDER "ITOM_PrivatePlugins")
    add_subdirectory(ITOM_PrivatePlugins)
endif(BUILD_ITOM_PRIVATEPLUGINS)

# ---------------------------------------------------------------------------
# Move the Qt AUTOMOC/AUTOUIC/AUTORCC helper targets (<Target>_autogen and
# <Target>_autogen_timestamp_deps) into a dedicated solution folder.
#
# The CMAKE_AUTOGEN_TARGETS_FOLDER variable alone is not reliable here
# (especially for the newer *_autogen_timestamp_deps targets and when targets
# are created in sub directories), therefore we walk over every target that has
# been defined and assign its FOLDER property explicitly. This must run AFTER
# all add_subdirectory() calls so that all targets already exist.
# ---------------------------------------------------------------------------
function(itom_group_autogen_targets dir)
    get_property(subdirs DIRECTORY "${dir}" PROPERTY SUBDIRECTORIES)
    foreach(subdir IN LISTS subdirs)
        itom_group_autogen_targets("${subdir}")
    endforeach()

    get_property(targets DIRECTORY "${dir}" PROPERTY BUILDSYSTEM_TARGETS)
    foreach(target IN LISTS targets)
        if(target MATCHES "_autogen(_timestamp_deps)?$")
            set_target_properties(${target} PROPERTIES FOLDER "autogen")
        endif()
    endforeach()
endfunction()

itom_group_autogen_targets("${CMAKE_CURRENT_SOURCE_DIR}")
