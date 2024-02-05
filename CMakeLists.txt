cmake_minimum_required(VERSION 3.1)
project(bvh11 VERSION 0.1.0)
set(CMAKE_CXX_STANDARD 11)

find_package(Eigen3)

file(GLOB HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp)
file(GLOB SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/bvh11.cpp)

option(BUILD_SHARED_LIBS "Build shared libs" OFF)

add_library(bvh11 ${SOURCES})
target_link_libraries(bvh11 
	PRIVATE
		Eigen3::Eigen)
target_include_directories(bvh11 
	PUBLIC 
		$<INSTALL_INTERFACE:include/>
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>)

install(FILES ${HEADERS} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/)
install(TARGETS bvh11 ARCHIVE DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

option(BVH11_BUILD_DEMOS "Build demos" OFF)

if(BVH11_BUILD_DEMOS)
	file(GLOB RESOURCE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/resources/*.bvh)

	set(THREEDIMUTIL_BUILD_DEMOS OFF CACHE INTERNAL "" FORCE)
	add_subdirectory(external/three-dim-util)

	add_subdirectory(demos/simple_demo)
	add_subdirectory(demos/visual_demo)

	enable_testing()
	add_test(NAME simple_demo COMMAND $<TARGET_FILE:simple_demo> ${CMAKE_CURRENT_SOURCE_DIR}/resources/131_03.bvh)
endif()

# Export and install targets and headers
include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})

install(EXPORT ${PROJECT_NAME}-targets
    FILE
        ${PROJECT_NAME}Targets.cmake
    NAMESPACE
        ${PROJECT_NAME}::
    DESTINATION
        ${INSTALL_CONFIGDIR})

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/installConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

# Install the header files
install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
    FILES_MATCHING PATTERN "*.h")

# Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

export(EXPORT ${PROJECT_NAME}-targets
    FILE ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::)

export(PACKAGE ${PROJECT_NAME})