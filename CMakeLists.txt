cmake_minimum_required(VERSION 3.1.3)
project(BPPrint CXX)
set(bpprint_VERSION 1.1)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS False)

# For standard install paths (CMAKE_INSTALL_*DIR)
include(GNUInstallDirs)

# CMake doesn't support Intel CXX standard until cmake 3.6
if("${CMAKE_CXX_COMPILER_ID}" MATCHES "Intel")
  if("${CMAKE_VERSION}" VERSION_LESS "3.6")
      add_compile_options(-std=c++${CMAKE_CXX_STANDARD})
  endif()
endif()

# Enable testing?
enable_testing()

# Set the compile options
if("${CMAKE_CXX_COMPILER_ID}" MATCHES "Intel")
    add_compile_options(-w3)

    #   383  : value copied to temporary, reference to temporary used
    #   981  : operands are evaluated in unspecified order
    #  1418  : external function definition with no prior declaration
    add_compile_options(-wd383)
    add_compile_options(-wd981)
    add_compile_options(-wd1418)
elseif("${CMAKE_CXX_COMPILER_ID}" MATCHES "GNU")
    add_compile_options(-Wall;-Wextra;-pedantic)
elseif("${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang")
    add_compile_options(-Wall;-Wextra;-pedantic)
    add_compile_options(-Wno-format-nonliteral)

    # for heavy-duty testing
    add_compile_options(-Weverything)
    add_compile_options(-Wno-c++98-compat)
    add_compile_options(-Wno-c++98-compat-pedantic)
    add_compile_options(-Wno-implicit-fallthrough)
    add_compile_options(-Wno-padded)
    add_compile_options(-Wno-weak-vtables)
    add_compile_options(-Wno-covered-switch-default)
endif()

# The main subdirectory with the bpprint library
add_subdirectory(bpprint)

# Directory with some tests
add_subdirectory(test)

####################################
# Exporting the CMake configuration
####################################
include(CMakePackageConfigHelpers)

configure_package_config_file(bpprintConfig.cmake.in
                              ${CMAKE_CURRENT_BINARY_DIR}/bpprintConfig.cmake
                              INSTALL_DESTINATION share/cmake/bpprint
)

write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/bpprintConfigVersion.cmake
                                 VERSION ${bpprint_VERSION}
                                 COMPATIBILITY AnyNewerVersion
)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/bpprintConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/bpprintConfigVersion.cmake
        DESTINATION share/cmake/bpprint
)

install(EXPORT bpprintTargets
        NAMESPACE bpprint::
        DESTINATION share/cmake/bpprint
)
