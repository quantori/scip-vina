cmake_minimum_required(VERSION 2.8.12)
project(vina)
set (CMAKE_CXX_STANDARD 14)
if (BUILD_SUB_CMAKE)
    if(UNIX AND NOT APPLE)
        set(OS "Linux")
    elseif(UNIX AND APPLE)
        set(OS "macOS")
    elseif(WIN32)
        set(OS "win32")
    endif()
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES "aarch64")
        set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}../../../../../DockingInterface/DockingInterface/C_Dynamic_Libs/${OS}/arm64)
    else()
        set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}../../../../../DockingInterface/DockingInterface/C_Dynamic_Libs/${OS}/${CMAKE_SYSTEM_PROCESSOR})
    endif()
endif()
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif(NOT CMAKE_BUILD_TYPE)
set(CMAKE_CXX_FLAGS_RELEASE "-O3")
include_directories(src/lib)

if (BOOST_ROOT)
    include_directories(${BOOST_ROOT}/include)
    include_directories(${BOOST_ROOT}/lib)
    set(Boost_INCLUDE_DIRS "${BOOST_ROOT}/include")
    set(Boost_NO_SYSTEM_PATHS TRUE)
    set(Boost_NO_BOOST_CMAKE TRUE)
endif()
find_package(Boost  COMPONENTS program_options system thread filesystem REQUIRED)
message("-- Found Boost include files at ${Boost_INCLUDE_DIRS}")
message("-- Found Boost libraries at ${Boost_LIBRARIES}")
set(LIBS ${Boost_LIBRARIES})


file(GLOB srcvina
        "src/lib/*.h"
        "src/lib/*.cpp"
        "src/main/main.cpp")



add_library(vinaDynamic SHARED
        ${srcvina})
set_target_properties(vinaDynamic PROPERTIES CXX_VISIBILITY_PRESET hidden)


if (NEEDEX)
    add_executable(vina ${srcvina} )
    target_link_libraries(vina ${LIBS})
endif()

target_link_libraries(vinaDynamic ${LIBS})
