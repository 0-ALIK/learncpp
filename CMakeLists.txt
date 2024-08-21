cmake_minimum_required(VERSION 3.22)
project(learncpp VERSION 0.1.0 LANGUAGES C CXX)


# Definiciones varias para deshabilitar funcionalidades innecesarias de GLFW
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(GLFW_INSTALL OFF CACHE BOOL "" FORCE)


# Definición de las carpetas de las librerías
add_subdirectory(library/GLFW)
add_subdirectory(library/glad)
add_subdirectory(library/glm)


# Definición de los archivos de código fuente
file(GLOB_RECURSE SRC CONFIGURE_DEPENDS 
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp" 
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
)


# Definición del ejecutable
add_executable("${CMAKE_PROJECT_NAME}" ${SRC})
set_property(TARGET "${CMAKE_PROJECT_NAME}" PROPERTY CXX_STANDARD 17)
target_compile_definitions("${CMAKE_PROJECT_NAME}" PUBLIC RESOURCES_PATH="${CMAKE_CURRENT_SOURCE_DIR}/resources/")
target_sources("${CMAKE_PROJECT_NAME}" PRIVATE ${SRC} )


# Incluir direcctorio de archivos de cabecera .h
target_include_directories("${CMAKE_PROJECT_NAME}" PUBLIC 
    "${CMAKE_CURRENT_SOURCE_DIR}/src"
)


# Definición de las librerías objetivo
target_link_libraries("${CMAKE_PROJECT_NAME}" PRIVATE glfw glad glm)


# Configuración específica para sistemas operativos
if (WIN32)
    target_compile_definitions("${CMAKE_PROJECT_NAME}" PRIVATE _CRT_SECURE_NO_WARNINGS)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
elseif (APPLE)
    find_library(COCOA_LIBRARY Cocoa)
    find_library(IOKIT_LIBRARY IOKit)
    find_library(COREVIDEO_LIBRARY CoreVideo)
    target_link_libraries("${CMAKE_PROJECT_NAME}" PRIVATE ${COCOA_LIBRARY} ${IOKIT_LIBRARY} ${COREVIDEO_LIBRARY})
elseif (UNIX)
    find_package(X11 REQUIRED)
    target_link_libraries("${CMAKE_PROJECT_NAME}" PRIVATE ${X11_LIBRARIES})
endif()


# Opciones de compilación para GCC y Clang
if (CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options("${CMAKE_PROJECT_NAME}" PRIVATE -Wall -Wextra -pedantic -Werror)
endif()


# Establecer el directorio de salida
set_target_properties("${CMAKE_PROJECT_NAME}" PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin"
)