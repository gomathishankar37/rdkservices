# If not stated otherwise in this file or this component's license file the
# following copyright and licenses apply:
#
# Copyright 2020 RDK Management
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

set(PLUGIN_NAME XCast)
set(MODULE_NAME ${NAMESPACE}${PLUGIN_NAME})

set(PLUGIN_XCAST_AUTOSTART "false" CACHE STRING "Automatically start XCast plugin")
set(PLUGIN_XCAST_STARTUPORDER "" CACHE STRING "To configure startup order of XCast plugin")
set(PLUGIN_XCAST_MODE "Local" CACHE STRING "Controls if the plugin should run in its own process, in process or remote")

find_package(${NAMESPACE}Plugins REQUIRED)

add_library(${MODULE_NAME} SHARED
        XCast.cpp
        XCastImplementation.cpp
        Module.cpp
        XCastManager.cpp)

find_package(RFC)
find_package(IARMBus)
find_package(GLIB REQUIRED)
find_package(WPEFrameworkSecurityUtil)
if(NOT WPEFRAMEWORK_SECURITYUTIL_FOUND)
    add_definitions(-DSECURITY_TOKEN_ENABLED=0)
endif()

find_library(XDIAL_LIBRARIES NAMES gdial-server)

set_target_properties(${MODULE_NAME} PROPERTIES
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED YES)

target_compile_definitions(${MODULE_NAME} PRIVATE MODULE_NAME=Plugin_${PLUGIN_NAME})

add_definitions (-DRT_PLATFORM_LINUX)
target_include_directories(${MODULE_NAME} PRIVATE ${IARMBUS_INCLUDE_DIRS} ../helpers)
target_include_directories(${MODULE_NAME} PRIVATE ${RFC_INCLUDE_DIRS} ../helpers)
target_include_directories(${MODULE_NAME} PRIVATE ${GLIB_INCLUDE_DIRS})

if(NOT RDK_SERVICES_L1_TEST)
	target_link_libraries(${MODULE_NAME} PRIVATE ${NAMESPACE}Plugins::${NAMESPACE}Plugins ${RFC_LIBRARIES} ${IARMBUS_LIBRARIES} ${XDIAL_LIBRARIES})
else(RDK_SERVICES_L1_TEST)
      target_link_libraries(${MODULE_NAME} PRIVATE ${NAMESPACE}Plugins::${NAMESPACE}Plugins ${RFC_LIBRARIES} ${IARMBUS_LIBRARIES})
endif()

install(TARGETS ${MODULE_NAME}
        DESTINATION lib/${STORAGE_DIRECTORY}/plugins)

write_config(${PLUGIN_NAME})
