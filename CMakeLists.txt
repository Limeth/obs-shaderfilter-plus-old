cmake_minimum_required (VERSION 2.8.12)
project (obs-shaderfilter-plus)

if (OBSSourcePath OR DEFINED ENV{OBSSourcePath})
    # Set already
else()
    message("OBSSourcePath is missing. Please set this variable to the location of the OBS source (e.g., /home/user/workspace/c/obs-studio/libobs).")
endif()

find_path(OBS_LIB_DIR
    NAMES libobs.so obs.dll obs.lib
    HINTS
        ${OBSSourcePath}/../build/libobs/
        ${OBSSourcePath}/../build/${_lib_suffix}-Debug/libobs/
        ${OBSSourcePath}/../build/x86-Debug/libobs/
        ${OBSSourcePath}/../build/${_lib_suffix}-Release/libobs/
        ${OBSSourcePath}/../build/x86-Release/libobs/
    PATHS /usr/lib /usr/local/lib /opt/local/lib /sw/lib)

# Source
file (GLOB SOURCES ${CMAKE_SOURCE_DIR}/src/*.c)

include_directories (include ${CMAKE_BINARY_DIR}/config)
add_library (${PROJECT_NAME} SHARED
	${SOURCES}
	${HEADER_FILES}
)

# libobs
include_directories(${OBSSourcePath})
include_directories(/usr/include/obs)
add_library (libobs SHARED IMPORTED)
set_property (TARGET libobs PROPERTY IMPORTED_LOCATION ${OBS_LIB_DIR}/libobs.so)
target_link_libraries (${PROJECT_NAME} libobs)

# Install the resources in `data`
install(DIRECTORY data/ DESTINATION /usr/share/obs/obs-plugins/${PROJECT_NAME})
# Install the shared object binary itself
install(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION /usr/lib/obs-plugins/)
