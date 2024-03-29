cmake_minimum_required (VERSION 2.8.12)
project (obs-shaderfilter)

if (OBSSourcePath OR DEFINED ENV{OBSSourcePath})
    # Set already
else()
    set(OBSSourcePath "F:/Projects/obs-studio/libobs/" CACHE PATH "Path to OBS source code (e.g., C:/Dev/obs-studio/libobs/)")
    message("OBSSourcePath is missing. Please set this variable to the location of the OBS source (e.g., C:/Dev/obs-studio/libobs/).")
endif()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set(_lib_suffix 64)
else()
	set(_lib_suffix 32)
endif()

find_path(OBS_LIB_DIR
    NAMES libobs.so obs.dll obs.lib
    HINTS	
		${OBSSourcePath}/../build/libobs/
		${OBSSourcePath}/../build/${_lib_suffix}-Debug/libobs/
		${OBSSourcePath}/../build/x86-Debug/libobs/		
		${OBSSourcePath}/../build/${_lib_suffix}-Release/libobs/		
		${OBSSourcePath}/../build/x86-Release/libobs/
		#C:/Users/Surn/CMakeBuilds/6fd9c833-c666-9735-93d7-33bcfdef5371/build/x64-${CMAKE_BUILD_TYPE}/libobs/
    PATHS
		/usr/lib /usr/local/lib /opt/local/lib /sw/lib)

if(!OBS_LIB_DIR)        
	set(OBS_LIB_DIR "C:/Users/Surn/CMakeBuilds/6fd9c833-c666-9735-93d7-33bcfdef5371/build/x64-${CMAKE_BUILD_TYPE}/libobs" CACHE PATH "Path to OBS lib.dll")
endif()
# Source
file (GLOB SOURCES ${CMAKE_SOURCE_DIR}/src/*.c)

include_directories (include ${CMAKE_BINARY_DIR}/config)
add_library (${PROJECT_NAME} SHARED
	${SOURCES}
	${HEADER_FILES}
)

# libobs
include_directories(${OBSSourcePath})
add_library (libobs SHARED IMPORTED)
if (WIN32)
    set_property (TARGET libobs PROPERTY IMPORTED_LOCATION ${OBS_LIB_DIR}/obs.dll)
    set_property (TARGET libobs PROPERTY IMPORTED_IMPLIB ${OBS_LIB_DIR}/obs.lib)
else()
    set_property (TARGET libobs PROPERTY IMPORTED_LOCATION ${OBS_LIB_DIR}/libobs.so)
endif()
target_link_libraries (${PROJECT_NAME} libobs)

if (WIN32)
    install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION ${PROJECT_NAME}/bin/${_lib_suffix}bit)
else()
    install(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION ${PROJECT_NAME}/bin/${_lib_suffix}bit)
endif()

install(FILES ${CMAKE_BINARY_DIR}/${CMAKE_BUILD_TYPE}/${PROJECT_NAME}.pdb DESTINATION ${PROJECT_NAME}/bin/${_lib_suffix}bit CONFIGURATIONS Debug)
install(DIRECTORY data/ DESTINATION ${PROJECT_NAME}/data/)
