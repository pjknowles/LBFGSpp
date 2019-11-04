project(LBFGSpp)
cmake_minimum_required(VERSION 3.15)
if (NOT TARGET Eigen3::Eigen)
    message("Eigen3::Eigen is not yet a target")
    find_package(Eigen3 3.3.0 NO_MODULE NO_CMAKE_ENVIRONMENT_PATH NO_CMAKE_PACKAGE_REGISTRY QUIET)
    if (EIGEN3_FOUND)
        message(STATUS "Eigen3 found on system: ${EIGEN3_INCLUDE_DIR}")
        add_library(eigen INTERFACE)
        target_include_directories(eigen INTERFACE
                $<BUILD_INTERFACE:${EIGEN3_INCLUDE_DIR}>
                $<INSTALL_INTERFACE:${EIGEN3_INCLUDE_DIR}>
                )
        add_library(Eigen3::Eigen ALIAS eigen)
    else ()
        message(STATUS "Eigen3::Eigen not found on system, and downloaded")
        get_dependency(eigen3 OFF)
        get_dependency_name("eigen3")
        FetchContent_GetProperties(${_dependency_name})
        add_library(eigen INTERFACE)
        set(EIGEN3_INCLUDE_DIR ${${_dependency_name}_SOURCE_DIR})
        target_include_directories(eigen INTERFACE
                $<BUILD_INTERFACE:${${_dependency_name}_SOURCE_DIR}>
                $<INSTALL_INTERFACE:include>
                )
    endif ()
    add_library(Eigen3::Eigen ALIAS eigen)
else ()
    message(STATUS "IterativeSolver: Eigen3::Eigen target already defined")
endif ()

foreach (e quadratic rosenbrock rosenbrock-bracketing rosenbrock-comparison)
    add_executable("example-${e}" "example-${e}.cpp")
    target_include_directories("example-${e}" PRIVATE include)
    target_link_libraries("example-${e}" Eigen3::Eigen)
endforeach ()