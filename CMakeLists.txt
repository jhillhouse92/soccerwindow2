cmake_minimum_required (VERSION 3.5)
project(soccerwindow2)

set(CMAKE_C_COMPILER "/usr/local/opt/llvm/bin/clang")
set(CMAKE_CXX_COMPILER "/usr/local/opt/llvm/bin/clang++")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations -Wno-unused-result")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-deprecated-declarations -Wno-unused-result")

set(CMAKE_CXX_STANDARD 11) # or higher, such as 14, 17, etc.
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(BOOST_ROOT "/usr/local/Cellar/boost/1.84.0_1")
set(BOOST_INCLUDEDIR "${BOOST_ROOT}/include")
set(BOOST_LIBRARYDIR "${BOOST_ROOT}/lib")
find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})

set(LIBRCSC_INCLUDE_DIR CACHE FILEPATH "librcsc include directory")
set(LIBRCSC_LINK_DIR CACHE FILEPATH "librcsc link directory")
if(NOT LIBRCSC_INCLUDE_DIR)
    message(FATAL_ERROR "LIBRCSC_INCLUDE_DIR required but not set.")
endif(NOT LIBRCSC_INCLUDE_DIR)
if(NOT LIBRCSC_LINK_DIR)
    message(FATAL_ERROR "LIBRCSC_LINK_DIR required but not set.")
endif(NOT LIBRCSC_LINK_DIR)
include_directories(${LIBRCSC_INCLUDE_DIR})
link_directories(${LIBRCSC_LINK_DIR})

# Set the path to the Qt5 installation
set(Qt5_DIR "/usr/local/opt/qt@5/lib/cmake/Qt5")

# Ensure that CMake finds the Qt5 version installed by Homebrew, not the system version
set(CMAKE_PREFIX_PATH ${Qt5_DIR})

find_package(Qt5 REQUIRED COMPONENTS Widgets Core Network)


set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
add_definitions(-DHAVE_CONFIG_H)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

set(RCSC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/rcsc)
set(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB MODEL_SOURCES ${SRC_DIR}/model/*.cpp)
file(GLOB WINDOW_SOURCES ${SRC_DIR}/qt4/*.cpp)
include_directories(${SRC_DIR}/model ${SRC_DIR}/qt4)

#include_directories(AFTER SYSTEM ${QT_INCLUDES} "/usr/local/opt/qt@4/include/QtGui" "/usr/local/opt/qt@4/include/QtCore" "/usr/local/opt/qt@4/include/QtNetwork")

# Display the QT_INCLUDES path
#message(STATUS "QT_INCLUDES: ${QT_INCLUDES}")

# Link directories might be needed explicitly in some configurations
#link_directories(${QT_LIBRARY_DIRS})

add_executable(soccerwindow2 ${MODEL_SOURCES} ${WINDOW_SOURCES})
target_link_libraries(soccerwindow2 Qt5::Widgets Qt5::Core Qt5::Network rcsc_agent rcsc_ann rcsc_geom rcsc_gz rcsc_net rcsc_param rcsc_rcg rcsc_time)
