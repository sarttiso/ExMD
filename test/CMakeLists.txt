#############################################
# DOWNLOAD AND INSTALL GTEST
#############################################

set(DOWNLOAD_GTEST_DIR ${PROJECT_SOURCE_DIR}/lib/download_gtest)

if (CMAKE_VERSION VERSION_LESS 3.2)
    set(UPDATE_DISCONNECTED_IF_AVAILABLE "")
else()
    set(UPDATE_DISCONNECTED_IF_AVAILABLE "UPDATE_DISCONNECTED 1")
endif()

include(${DOWNLOAD_GTEST_DIR}/DownloadProject.cmake)
download_project(PROJ                googletest
                 GIT_REPOSITORY      https://github.com/google/googletest.git
                 GIT_TAG             master
                 ${UPDATE_DISCONNECTED_IF_AVAILABLE}
)

# Prevent GoogleTest from overriding our compiler/linker options
# when building with Visual Studio
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})

# When using CMake 2.8.11 or later, header path dependencies
# are automatically added to the gtest and gmock targets.
# For earlier CMake versions, we have to explicitly add the
# required directories to the header search path ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
    include_directories("${gtest_SOURCE_DIR}/include"
                        "${gmock_SOURCE_DIR}/include")
endif()

# Include the gtest library
include_directories(${gtest_SOURCE_DIR}/include
                    ${gtest_SOURCE_DIR}
                    ${PROJECT_SOURCE_DIR}/src
)


#############################################
# UNIT TEST
#############################################
set(TEST ${TEST} test_physics.cpp test_distance.cpp test_gravity.cpp
                 test_simulation.cpp test_flocking.cpp test_drag.cpp)

add_executable(runUnitTests ${TEST})

# Standard linkning to gtest
target_link_libraries(runUnitTests gtest gtest_main)

# Extra linking for the project
target_link_libraries(runUnitTests exmd_lib)

# Global Variables
set(GLOBAL_TEST ${TEST} PARENT_SCOPE)
