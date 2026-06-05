cmake_minimum_required(VERSION 3.10)

project(CNPY)

set(LIBZIPPP_HEADER "${CMAKE_CURRENT_SOURCE_DIR}/extern/libzippp/zip.hpp")
if(NOT EXISTS "${LIBZIPPP_HEADER}")
    message(FATAL_ERROR
        "Missing cnpy dependency: ${LIBZIPPP_HEADER}. "
        "Initialize repository submodules before configuring the build.")
endif()

option(ENABLE_STATIC "Build static (.a) library" ON)

SET(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

#if(ENABLE_STATIC)
    add_library(cnpy STATIC "cnpy.cpp")
    set_target_properties(cnpy PROPERTIES OUTPUT_NAME "cnpy")
    install(TARGETS "cnpy"
        EXPORT commonsTargets
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include
    )
    install(FILES
        "${CMAKE_CURRENT_SOURCE_DIR}/cnpy.h"
        "${CMAKE_CURRENT_SOURCE_DIR}/extern/libzippp/zip.hpp"
        DESTINATION include/cnpy
    )
#else()
#    add_library(cnpy SHARED "cnpy.cpp")

#    install(TARGETS "cnpy" LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
#endif(ENABLE_STATIC)

message(STATUS "lip ${LIBZIP_INCLUDE_DIR} ${LIBZIP_LIBRARY}")
target_include_directories(cnpy PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
  "$<BUILD_INTERFACE:${LIBZIP_INCLUDE_DIR}>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/extern/libzippp>"
  "$<INSTALL_INTERFACE:include>"
)

target_link_libraries(cnpy PUBLIC Commons::LibZIP Commons::LibZ)
target_compile_features(cnpy PRIVATE cxx_std_14)
add_dependencies(cnpy Commons::LibZIP Commons::LibZ)
