# If not stated otherwise in this file or this component's LICENSE file the
# following copyright and licenses apply:
#
# Copyright 2020 Metrological
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

project(CryptographyExtAccess)

cmake_minimum_required(VERSION 3.3)

find_package(WPEFramework)

project_version(1.0.0)

set(MODULE_NAME ${NAMESPACE}${PROJECT_NAME})

message("Setup ${MODULE_NAME} v${PROJECT_VERSION}")

set(PLUGIN_CRYPTOGRAPHYEXTACCESS_AUTOSTART "true" CACHE STRING "Automatically start CryptographyExtAccess plugin")
set(PLUGIN_CRYPTOGRAPHYEXTACCESS_MODE "Off" CACHE STRING "Controls if the plugin should run in its own process, in process or remote.")

# deprecated/legacy flags support
if(PLUGIN_CRYPTOGRAPHYEXTACCESS_OUTOFPROCESS STREQUAL "true")
    set(PLUGIN_CRYPTOGRAPHYEXTACCESS_MODE "Local" CACHE STRING "Process mode"  FORCE)
    unset(PLUGIN_CRYPTOGRAPHYEXTACCESS_OUTOFPROCESS CACHE)
endif()

find_package(${NAMESPACE}Plugins REQUIRED)
find_package(${NAMESPACE}Definitions REQUIRED)
find_package(CompileSettingsDebug CONFIG REQUIRED)
find_package(${NAMESPACE}Cryptography CONFIG REQUIRED)

find_package(${NAMESPACE}Definitions REQUIRED)

add_library(${MODULE_NAME} SHARED
    Module.cpp
    CryptographyExtAccess.cpp
    CryptographyRPC.cpp
)

set_target_properties(${MODULE_NAME} PROPERTIES
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED YES)

target_link_libraries(${MODULE_NAME}
    PRIVATE
        CompileSettingsDebug::CompileSettingsDebug
        ${NAMESPACE}Plugins::${NAMESPACE}Plugins
        ${NAMESPACE}Definitions::${NAMESPACE}Definitions
        ${NAMESPACE}Cryptography::${NAMESPACE}Cryptography
)

install(TARGETS ${MODULE_NAME}
    DESTINATION lib/${STORAGE_DIRECTORY}/plugins)

write_config()
