cmake_minimum_required(VERSION 3.18 FATAL_ERROR)
cmake_policy(VERSION 3.18)

project(lu_base_ws)

#

set(CPM_SOURCE_CACHE "${CMAKE_CURRENT_SOURCE_DIR}/out/.cpm" CACHE PATH "")
include(cmake/CPM.cmake)
include(cmake/PackageProject.cmake)

#

CPMAddPackage(
    NAME DirectX-Headers
    GITHUB_REPOSITORY "microsoft/DirectX-Headers"
    GIT_TAG "1a0c980fce8407ae5fced0a592cf56010770f7a7")

packageProject(
    NAME DirectX-Headers
    VERSION 0.0.0
    BINARY_DIR ${DirectX-Headers_BINARY_DIR}
    INCLUDE_DIR ${DirectX-Headers_SOURCE_DIR}/include
    INCLUDE_DESTINATION include
    INCLUDE_HEADER_PATTERN "*.h"
    COMPATIBILITY AnyNewerVersion
    DISABLE_VERSION_SUFFIX YES)

packageProject(
    NAME DirectX-Guids
    VERSION 0.0.0
    BINARY_DIR ${DirectX-Headers_BINARY_DIR}
    INCLUDE_DIR ${DirectX-Headers_SOURCE_DIR}/include
    INCLUDE_DESTINATION include
    # Disable since this is lib only
    INCLUDE_HEADER_PATTERN "*.z"
    COMPATIBILITY AnyNewerVersion
    DISABLE_VERSION_SUFFIX YES)

#

CPMAddPackage(
    NAME CrossWindow
    GITHUB_REPOSITORY "alaingalvan/CrossWindow"
    GIT_TAG "3fb97e884ecf27a55adb68cec9def10516a48131"
    PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/patches/CrossWindow.patch
    OPTIONS
        "XWIN_API ${LU_API}"
        "XWIN_OS ${LU_OS}"
        "XWIN_API ${LU_API}"
        "XWIN_API_PATH ${LU_API_PATH}")

packageProject(
    # the name of the target to export
    NAME CrossWindow
    # the version of the target to export
    VERSION 0.0.0
    # a temporary directory to create the config files
    BINARY_DIR ${CrossWindow_BINARY_DIR}
    # location of the target's public headers
    INCLUDE_DIR ${CrossWindow_SOURCE_DIR}/src
    # should match the target's INSTALL_INTERFACE include directory
    INCLUDE_DESTINATION include/CrossWindow-0.0.0
    # (optional) option to install only header files with matching pattern
    INCLUDE_HEADER_PATTERN "*.h"
    # semicolon separated list of the project's dependencies
    #DEPENDENCIES "fmt 7.1.3;cxxopts 2.2.0"
    # (optional) create a header containing the version info
    # Note: that the path to headers should be lowercase
    #VERSION_HEADER "${PROJECT_NAME}/version.h"
    # (optional) create a export header using GenerateExportHeader module
    #EXPORT_HEADER "${PROJECT_NAME}/export.h"
    # (optional) install your library with a namespace (Note: do NOT add extra '::')
    #NAMESPACE ${PROJECT_NAMESPACE}
    # (optional) define the project's version compatibility, defaults to `AnyNewerVersion`
    # supported values: `AnyNewerVersion|SameMajorVersion|SameMinorVersion|ExactVersion`
    COMPATIBILITY AnyNewerVersion
    # (optional) option to disable the versioning of install destinations
    DISABLE_VERSION_SUFFIX YES
    # (optional) option to ignore target architecture for package resolution
    # defaults to YES for header only (i.e. INTERFACE) libraries
    #ARCH_INDEPENDENT YES
)

#

CPMAddPackage(
    NAME CrossWindowGraphics
    GITHUB_REPOSITORY "alaingalvan/CrossWindow-Graphics"
    GIT_TAG "28022e3ba565a975e8bf6dada1aa605c6f73a848"
    PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/patches/CrossWindowGraphics.patch
    OPTIONS
        "XWIN_API ${LU_API}"
        "XWIN_OS ${LU_OS}"
        "XWIN_API ${LU_API}"
        "XWIN_API_PATH ${LU_API_PATH}"
        "XGFX_API ${LU_GFX_API}")

packageProject(
    NAME CrossWindowGraphics
    VERSION 0.0.0
    BINARY_DIR ${CrossWindowGraphics_BINARY_DIR}
    INCLUDE_DIR ${CrossWindowGraphics_SOURCE_DIR}/src
    INCLUDE_DESTINATION include/CrossWindowGraphics-0.0.0
    INCLUDE_HEADER_PATTERN "*.h"
    COMPATIBILITY AnyNewerVersion
    DISABLE_VERSION_SUFFIX YES)

#

CPMAddPackage(
    NAME FlyCube
    GITHUB_REPOSITORY "andrejnau/FlyCube"
    GIT_TAG "6255faa5d69acd1d592c74374b116ce2a39394a1"
    PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/patches/FlyCube.patch)
    #OPTIONS
    #    "XGFX_API ${LU_GFX_API}")

#

# Ensure this is not overwritten
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_base")
    set(FETCHCONTENT_SOURCE_DIR_LU_BASE "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_base")
endif()

CPMAddPackage(
    NAME lu_base
    GITHUB_REPOSITORY "loopunit/lu_base"
    GIT_TAG "dbe9930031541f6e9472e8f495cd295dc79ef89d"
    SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_base"
    OPTIONS 
        "ENABLE_TESTING FALSE")

packageProject(
    NAME lu_base
    NAMESPACE loopunit
    VERSION 0.0.0
    BINARY_DIR ${lu_base_BINARY_DIR}
    INCLUDE_DIR ${lu_base_SOURCE_DIR}/include
    INCLUDE_DESTINATION include/lu_base-0.0.0
    INCLUDE_HEADER_PATTERN "*.h"
    DEPENDENCIES "CrossWindow 0.0.0"
    COMPATIBILITY AnyNewerVersion
    DISABLE_VERSION_SUFFIX YES)

#

# Ensure this is not overwritten
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_main")
    set(FETCHCONTENT_SOURCE_DIR_LU_MAIN "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_main")
endif()

CPMAddPackage(
    NAME lu_main
    NAMESPACE loopunit
    GITHUB_REPOSITORY "loopunit/lu_main"
    GIT_TAG "6918c9d351ab767aee8031f602bb3a3bd0f6ee6c"
    SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/dev/lu_main"
    OPTIONS 
        "ENABLE_TESTING FALSE")

packageProject(
    NAME lu_main
    VERSION 0.0.0
    BINARY_DIR ${lu_main_BINARY_DIR}
    INCLUDE_DIR ${lu_main_SOURCE_DIR}/include
    INCLUDE_DESTINATION include/lu_main-0.0.0
    INCLUDE_HEADER_PATTERN "*.h"
    DEPENDENCIES "CrossWindow 0.0.0"
    COMPATIBILITY AnyNewerVersion
    DISABLE_VERSION_SUFFIX YES)

#

add_subdirectory(${lu_base_SOURCE_DIR}/test)
#add_subdirectory(${lu_main_SOURCE_DIR}/test)
