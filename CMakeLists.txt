cmake_minimum_required(VERSION 3.11..)

project(sep C)

LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake_modules/")
include(GNUInstallDirs)

set(SOURCES
   ${CMAKE_SOURCE_DIR}/src/analyse.c
   ${CMAKE_SOURCE_DIR}/src/convolve.c
   ${CMAKE_SOURCE_DIR}/src/deblend.c
   ${CMAKE_SOURCE_DIR}/src/extract.c
   ${CMAKE_SOURCE_DIR}/src/lutz.c
   ${CMAKE_SOURCE_DIR}/src/aperture.c
   ${CMAKE_SOURCE_DIR}/src/background.c
   ${CMAKE_SOURCE_DIR}/src/util.c
   )

include_directories(${CMAKE_INCLUDE_PATH} ${CMAKE_SOURCE_DIR}/src ${CFITSIO_INCLUDE_DIR})

link_directories(${CMAKE_LIBRARY_PATH} ${CMAKE_SOURCE_DIR}/src)

add_library(sep SHARED ${SOURCES})
set_target_properties(sep PROPERTIES OUTPUT_NAME sep)
set_target_properties(sep PROPERTIES VERSION 0.6.0 SOVERSION 0)
set_target_properties(sep PROPERTIES C_VISIBILITY_PRESET hidden)

if (MSVC)
   add_definitions(-D_USE_MATH_DEFINES)
else ()
   add_compile_options(-Wcast-qual)
   target_link_libraries(sep m)
endif()

install(TARGETS sep LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(FILES ${CMAKE_SOURCE_DIR}/src/sep.h DESTINATION include)


# -------------------------------
# Generate pkg-config file
# -------------------------------
set(prefix "${CMAKE_INSTALL_PREFIX}")
set(exec_prefix "\${prefix}")
set(libdir "\${exec_prefix}/${CMAKE_INSTALL_LIBDIR}")
set(includedir "\${prefix}/${CMAKE_INSTALL_INCLUDEDIR}")

# Configure the .pc file
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/sep.pc.in
               ${CMAKE_CURRENT_BINARY_DIR}/sep.pc
               @ONLY)

# Install the .pc file
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/sep.pc
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)

