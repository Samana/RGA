cmake_minimum_required (VERSION 2.6)
project (RadeonGPUAnalyzerCLI)

# The version number
set (RadeonGPUAnalyzerCLI_VERSION_MAJOR 1)
set (RadeonGPUAnalyzerCLI_VERSION_MINOR 1)

# Pass the build number
if (NOT "$ENV{BUILD_NUMBER}" STREQUAL "")
    add_definitions(-DRGA_BUILD_NUMBER=$ENV{BUILD_NUMBER})
endif()

# Windows-specific preprocessor directives
if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

# Identify if this script runs on a Debian system
set (IsDebian FALSE)
if(EXISTS "/etc/debian_version")
    set(IsDebian TRUE)
endif()

# Add this definitions if static linkage is used with the other projects
add_definitions(-DRGA_BACKEND_STATIC -DAMDTBASETOOLS_STATIC -DAMDTOSWRAPPERS_STATIC -DAMDTOSAPIWRAPPERS_STATIC)
 
# Configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/RadeonGPUAnalyzerCLIConfig.h.in"
  "${PROJECT_BINARY_DIR}/RadeonGPUAnalyzerCLIConfig.h"
  ) 

# Add the binary tree to the search path for include files
# so that we will find ProjectConfig.h
include_directories("${PROJECT_BINARY_DIR}")

# Common include directories
include_directories("${PROJECT_SOURCE_DIR}/../RadeonGPUAnalyzerBackend/include/Common")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src")
include_directories("${PROJECT_SOURCE_DIR}/../")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/CELF/Include")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/DynamicLibraryModule")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Lib/AMD/ACL/TOT/include")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Lib/AMD/CAL/8.95/include")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Lib/AMD/APPSDK/3-0/include")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/TSingleton")
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/DeviceInfo")
if (NOT IsDebian)
# On Debian we use the native Boost libraries
include_directories("${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0")
endif()

# Windows-specific include directories
if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
    include_directories("${PROJECT_SOURCE_DIR}/../Core/DX10ASM/Include")
    include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/ShaderUtils")
    include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/ShaderUtils/DX10")
    include_directories("${PROJECT_SOURCE_DIR}/../../Common/Src/ShaderUtils/DX11")
endif()


# Source files that are common to Windows and Linux
file(GLOB COMMON_SRC
    "src/*.cpp"
    "src/*.h"
)

# Pick up the source files that are relevant to the platform
if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
    add_executable(RadeonGPUAnalyzerCLI ${COMMON_SRC} ${WINDOWS_SRC})
    if (CMAKE_BUILD_TYPE STREQUAL "Release")
        # Release build
        if (CMAKE_64BIT_TARGET)
            # 64-bit target
            find_library(BOOST_LIBRARY1 libboost_filesystem-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY2 libboost_system-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY3 libboost_program_options-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY4 libboost_regex-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
        else()
            # 32-bit target
            find_library(BOOST_LIBRARY1 libboost_filesystem-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY2 libboost_system-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY3 libboost_program_options-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY4 libboost_regex-vc140-mt-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
        endif()
    else()
        # Debug build
        if (CMAKE_64BIT_TARGET)
            # 64-bit target
            find_library(BOOST_LIBRARY1 libboost_filesystem-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY2 libboost_system-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY3 libboost_program_options-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
            find_library(BOOST_LIBRARY4 libboost_regex-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86_64")
        else()
            # 32-bit target
            find_library(BOOST_LIBRARY1 libboost_filesystem-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY2 libboost_system-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY3 libboost_program_options-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
            find_library(BOOST_LIBRARY4 libboost_regex-vc140-mt-gd-1_59 HINTS "${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/VC14/x86")
        endif()
    endif()
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    # DeviceInfoLib objects are defined in RadeonGPUAnalyzerBackend/CMakeLists.txt
    add_executable(RadeonGPUAnalyzerCLI ${COMMON_SRC} ${LINUX_SRC} $<TARGET_OBJECTS:DeviceInfoLib>)
    set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES ENABLE_EXPORTS ON)

    if(IsDebian)
        # Debian-specific
        # Configure Boost link settings 
        set(Boost_USE_STATIC_LIBS ON) 
        set(Boost_USE_MULTITHREADED ON)  
        set(Boost_USE_STATIC_RUNTIME OFF)

        # We use 1.55 as the minimum version
        find_package(Boost 1.55.0 COMPONENTS filesystem system regex program_options) 

        if(Boost_FOUND)
            include_directories(${Boost_INCLUDE_DIRS}) 
        endif()
    else()
        # RHEL
        find_library(BOOST_LIBRARY1 libboost_filesystem.a HINTS ${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/RHEL6/x86_64)
        find_library(BOOST_LIBRARY2 libboost_system.a HINTS ${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/RHEL6/x86_64)
        find_library(BOOST_LIBRARY3 libboost_program_options.a HINTS ${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/RHEL6/x86_64)
        find_library(BOOST_LIBRARY4 libboost_regex.a HINTS ${PROJECT_SOURCE_DIR}/../../Common/Lib/Ext/Boost/boost_1_59_0/lib/RHEL6/x86_64)
    endif()
endif()

# Set the name of the executable
if(AMD_INTERNAL)
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
        set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES OUTPUT_NAME rga-internal)
    elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        # On Linux we have a launch script that configures the environment prior to launching the executable.
        set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES OUTPUT_NAME rga-bin-internal)
    endif()
else()
    if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
        set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES OUTPUT_NAME rga)
    elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        # On Linux we have a launch script that configures the environment prior to launching the executable.
        set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES OUTPUT_NAME rga-bin)
    endif()
endif()

# Set binary suffix
set_target_properties(RadeonGPUAnalyzerCLI PROPERTIES DEBUG_POSTFIX -d)

# Link settings
if(NOT IsDebian)
target_link_libraries (RadeonGPUAnalyzerCLI RadeonGPUAnalyzerBackend AMDTOSAPIWrappers AMDTOSWrappers AMDTBaseTools ${BOOST_LIBRARY1} ${BOOST_LIBRARY2} ${BOOST_LIBRARY3} ${BOOST_LIBRARY4})
else()
target_link_libraries (RadeonGPUAnalyzerCLI RadeonGPUAnalyzerBackend AMDTOSAPIWrappers AMDTOSWrappers AMDTBaseTools ${Boost_LIBRARIES})
endif()

# Configure the post-build actions.
set(INTERNAL_ARG "")
if(AMD_INTERNAL)
    set(INTERNAL_ARG "-internal")
endif()

if(${CMAKE_SYSTEM_NAME} STREQUAL "Windows")
    if (CMAKE_64BIT_TARGET)
        set(COPY_SCRIPT_NAME "rga_copy_x64.bat")
    else()
        set(COPY_SCRIPT_NAME "rga_copy_x86.bat")
    endif()

    add_custom_command(TARGET RadeonGPUAnalyzerCLI
            POST_BUILD
            COMMAND ${PROJECT_SOURCE_DIR}/../Build/VS2015/${COPY_SCRIPT_NAME} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${CMAKE_BUILD_TYPE} ${INTERNAL_ARG}
            WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/../Build/VS2015
            )
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    add_custom_command(TARGET RadeonGPUAnalyzerCLI
            POST_BUILD
            COMMAND /bin/sh ${PROJECT_SOURCE_DIR}/../Build/linux/copy_post_build.sh ${INTERNAL_ARG}
            WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/../Build/linux
            )
endif()
