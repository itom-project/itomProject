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


cmake_minimum_required(VERSION 3.1.0)

project(itomproject VERSION 2.2.0)

message(
    STATUS "------------------- PROJECT itomproject ---------------------")
message(
    STATUS "------------------- Version = ${CMAKE_PROJECT_VERSION} -----------------------\n")

add_subdirectory(itom)
add_subdirectory(designerplugins)
add_subdirectory(plugins)

