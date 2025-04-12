cmake_minimum_required(VERSION 3.10)
project(sparkcan VERSION 1.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Define library targets as shared
add_library(sparkcan SHARED
    src/PIDController.cpp
    src/SparkBase.cpp
    src/SparkMax.cpp 
    src/SparkFlex.cpp
)

# Set include directories
target_include_directories(sparkcan PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# Define the executables
add_executable(example_control examples/control.cpp)
add_executable(example_pid examples/pid.cpp)
add_executable(example_status examples/status.cpp)

# Link the executables with the sparkmax-can library
target_link_libraries(example_control PRIVATE sparkcan)
target_link_libraries(example_pid PRIVATE sparkcan)
target_link_libraries(example_status PRIVATE sparkcan)

# Install the library
install(TARGETS sparkcan
    EXPORT sparkcanTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)

# Install the header files
install(DIRECTORY include/ DESTINATION include)

# Export the targets
install(EXPORT sparkcanTargets
    FILE sparkcanTargets.cmake
    NAMESPACE sparkcan::
    DESTINATION lib/cmake/sparkcan
)

# Create and install the package configuration file
include(CMakePackageConfigHelpers)
configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/sparkcanConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/sparkcanConfig.cmake"
    INSTALL_DESTINATION lib/cmake/sparkcan
)

# Generate the version file for the package
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/sparkcanConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

# Install the config and version files
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/sparkcanConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/sparkcanConfigVersion.cmake"
    DESTINATION lib/cmake/sparkcan
)

include(InstallRequiredSystemLibraries)

# Set the license file (adjust the name or remove if not applicable)
# set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")

# Set package metadata.
set(CPACK_PACKAGE_NAME "sparkcan")
set(CPACK_PACKAGE_VENDOR "ASTRO @ VT")
set(CPACK_PACKAGE_CONTACT "cameronw23@vt.edu")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "FORK of Sparkcan C++ library")
set(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})

# Set the maintainer field for the DEB package.
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Cameron") 

# Specify Debian package specifics.
set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "amd64")  # Or "all", "i386", etc.
set(CPACK_DEBIAN_PACKAGE_SECTION "libs")
set(CPACK_DEBIAN_PACKAGE_PRIORITY "optional")

# Optionally, specify dependencies the package might need:
# set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6 (>= 2.27)")

# Include CPack to generate the package targets.
include(CPack)