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

add_subdirectory(itom)
add_subdirectory(designerplugins)
add_subdirectory(plugins)
