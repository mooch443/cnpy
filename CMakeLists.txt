CMAKE_MINIMUM_REQUIRED(VERSION 3.5)

project(CNPY)

# Update submodules as needed
find_package(Git QUIET)
if(GIT_FOUND)
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()

option(ENABLE_STATIC "Build static (.a) library" ON)

SET(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

#if(ENABLE_STATIC)
    add_library(cnpy STATIC "cnpy.cpp")
    set_target_properties(cnpy PROPERTIES OUTPUT_NAME "cnpy")
    install(TARGETS "cnpy" ARCHIVE DESTINATION lib)
#else()
#    add_library(cnpy SHARED "cnpy.cpp")

#    install(TARGETS "cnpy" LIBRARY DESTINATION lib PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
#endif(ENABLE_STATIC)

message(STATUS "lip ${LIBZIP_INCLUDE_DIR} ${LIBZIP_LIBRARY}")
target_include_directories(cnpy PUBLIC ${LIBZIP_INCLUDE_DIR})
target_include_directories(cnpy PUBLIC "extern/libzippp/")

target_link_libraries(cnpy PRIVATE Commons::LibZIP Commons::LibZ)
target_compile_features(cnpy PRIVATE cxx_std_14)
add_dependencies(cnpy Commons::LibZIP Commons::LibZ)


target_include_directories(cnpy INTERFACE
  "$<BUILD_INTERFACE:${LIBZIP_INCLUDE_DIR};${CMAKE_CURRENT_SOURCE_DIR}/extern/libzippp>"
  "$<INSTALL_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>"
)
set_target_properties(cnpy PROPERTIES
  IMPORTED_LOCATION cnpy
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR};${LIBZIP_INCLUDE_DIR};${CMAKE_CURRENT_SOURCE_DIR}/extern/libzippp"
  INTERFACE_LINK_DIRECTORIES "${CMAKE_CURRENT_BINARY_DIR}"
)