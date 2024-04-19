cmake_minimum_required (VERSION 3.5)
project(soccerwindow2)

set(CMAKE_C_COMPILER "/usr/local/opt/llvm/bin/clang")
set(CMAKE_CXX_COMPILER "/usr/local/opt/llvm/bin/clang++")

set(CMAKE_CXX_STANDARD 11) # or higher, such as 14, 17, etc.
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(BOOST_ROOT "/usr/local/Cellar/boost/1.83.0")
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

message(STATUS "QT_MOC_EXECUTABLE: $ENV{QT_MOC_EXECUTABLE}")
message(STATUS "QT_RCC_EXECUTABLE: $ENV{QT_RCC_EXECUTABLE}")
set(QT_UIC_EXECUTABLE "/usr/local/opt/qt@4/bin/uic")
set(QT_QMAKE_EXECUTABLE "/usr/local/opt/qt@4/bin/qmake")
set(QT_MOC_EXECUTABLE "/usr/local/opt/qt@4/bin/moc")
set(QT_RCC_EXECUTABLE "/usr/local/opt/qt@4/bin/rcc")
set(QT_MKSPECS_DIR "/usr/local/opt/qt@4/etc/qt4/mkspecs")
find_package(Qt4 REQUIRED COMPONENTS QtCore QtGui QtNetwork)

set(QT_INCLUDES "/usr/local/opt/qt@4/include")     # Set the Qt4 includes
set(QT_LIBRARY_DIRS "/usr/local/opt/qt@4/lib")

find_library(QT_CORE NAMES QtCore PATHS ${QT_LIBRARY_DIRS} NO_DEFAULT_PATH)
find_library(QT_GUI_LIBRARY NAMES QtGui PATHS ${QT_LIBRARY_DIRS} NO_DEFAULT_PATH)
find_library(QT_NETWORK_LIBRARY NAMES QtNetwork PATHS ${QT_LIBRARY_DIRS} NO_DEFAULT_PATH)

set(CMAKE_AUTOMOC ON)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
add_definitions(-DHAVE_CONFIG_H)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

set(RCSC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/rcsc)
set(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB MODEL_SOURCES ${SRC_DIR}/model/*.cpp)
file(GLOB WINDOW_SOURCES ${SRC_DIR}/qt4/*.cpp)
include_directories(${SRC_DIR}/model ${SRC_DIR}/qt4)

include_directories(AFTER SYSTEM ${QT_INCLUDES} "/usr/local/opt/qt@4/include/QtGui")

# Display the QT_INCLUDES path
message(STATUS "QT_INCLUDES: ${QT_INCLUDES}")

# Link directories might be needed explicitly in some configurations
link_directories(${QT_LIBRARY_DIRS})

add_executable(soccerwindow2 ${MODEL_SOURCES} ${WINDOW_SOURCES})
target_link_libraries(soccerwindow2 ${QT_CORE} ${QT_GUI_LIBRARY} ${QT_NETWORK_LIBRARY} rcsc_agent rcsc_ann rcsc_geom rcsc_gz rcsc_net rcsc_param rcsc_rcg rcsc_time)
