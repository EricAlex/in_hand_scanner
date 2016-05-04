cmake_minimum_required(VERSION 2.6 FATAL_ERROR)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}")

#set(SUBSUBSYS_NAME in_hand_scanner)
#set(SUBSUBSYS_DESC "In-hand scanner for small objects")
#set(SUBSUBSYS_DEPS     common     features     io     kdtree apps)

################################################################################
# Qt
find_package(Qt4 REQUIRED)

# OpenGL
find_package(GLUT REQUIRED)
find_package(OpenGL REQUIRED)

#OpenNI
find_package(OpenNI2 REQUIRED)

find_package(PCL 1.6.0 REQUIRED)

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})


################################################################################

set(INCS
    "boost.h"
    "common_types.h"
    "eigen.h"
    "icp.h"
    "input_data_processing.h"
    "integration.h"
    "mesh_processing.h"
    "utils.h"
    "visibility_confidence.h"
)

set(IMPL_INCS
    "impl/common_types.hpp"
)

set(SRCS
    "help_window.cpp"
    "main.cpp"
    "main_window.cpp"
    "icp.cpp"
    "in_hand_scanner.cpp"
    "input_data_processing.cpp"
    "integration.cpp"
    "mesh_processing.cpp"
    "opengl_viewer.cpp"
    "visibility_confidence.cpp"
)

# Qt
set(MOC_IN_HAND_SCANNER_INC     "in_hand_scanner.h")
set(MOC_OPENGL_VIEWER_INC       "opengl_viewer.h")
set(MOC_OFFLINE_INTEGRATION_INC "offline_integration.h")

set(MOC_MAIN_WINDOW_INC "main_window.h")
set(MOC_HELP_WINDOW_INC "help_window.h")
set(UI_MAIN_WINDOW      main_window.ui)
set(UI_HELP_WINDOW      help_window.ui)

# Offline integration
set(OI_INCS
    "integration.h"
    "visibility_confidence.h"
)

set(OI_SRCS
    "main_offline_integration.cpp"
    "integration.cpp"
    "offline_integration.cpp"
    "opengl_viewer.cpp"
    "visibility_confidence.cpp"
)

################################################################################
# Qt
# http://qtnode.net/wiki/Qt4_with_cmake
# http://qt-project.org/quarterly/view/using_cmake_to_build_qt_projects
set(QT_USE_QTOPENGL TRUE)
include("${QT_USE_FILE}")
qt4_wrap_cpp(MOC_IN_HAND_SCANNER_SRC     "${MOC_IN_HAND_SCANNER_INC}")
qt4_wrap_cpp(MOC_OPENGL_VIEWER_SRC       "${MOC_OPENGL_VIEWER_INC}" OPTIONS -DBOOST_NO_TEMPLATE_PARTIAL_SPECIALIZATION)
qt4_wrap_cpp(MOC_OFFLINE_INTEGRATION_SRC "${MOC_OFFLINE_INTEGRATION_INC}" OPTIONS -DBOOST_NO_TEMPLATE_PARTIAL_SPECIALIZATION)

qt4_wrap_cpp(MOC_MAIN_WINDOW_SRC "${MOC_MAIN_WINDOW_INC}")
qt4_wrap_cpp(MOC_HELP_WINDOW_SRC "${MOC_HELP_WINDOW_INC}")
qt4_wrap_ui(UI_MAIN_WINDOW_INC   "${UI_MAIN_WINDOW}")
qt4_wrap_ui(UI_HELP_WINDOW_INC   "${UI_HELP_WINDOW}")

list(APPEND ADDITIONAL_LIBS ${QT_LIBRARIES})
include_directories("${CMAKE_CURRENT_BINARY_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}/include")

# In-hand scanner
list(APPEND INCS "${MOC_IN_HAND_SCANNER_INC}" "${MOC_OPENGL_VIEWER_INC}" "${MOC_MAIN_WINDOW_INC}" "${MOC_HELP_WINDOW_INC}" "${UI_MAIN_WINDOW_INC}" "${UI_HELP_WINDOW_INC}")
list(APPEND SRCS "${MOC_IN_HAND_SCANNER_SRC}" "${MOC_OPENGL_VIEWER_SRC}" "${MOC_MAIN_WINDOW_SRC}" "${MOC_HELP_WINDOW_SRC}")

set(EXE_NAME "pcl_in_hand_scanner")
ADD_EXECUTABLE(${EXE_NAME} ${SRCS} ${INCS} ${IMPL_INCS})
target_link_libraries("${EXE_NAME}" ${PCL_LIBRARIES} ${OPENGL_LIBRARIES} ${QT_LIBRARIES})

# Offline integration
list(APPEND OI_INCS "${MOC_OPENGL_VIEWER_INC}" "${MOC_OFFLINE_INTEGRATION_INC}")
list(APPEND OI_SRCS "${MOC_OPENGL_VIEWER_SRC}" "${MOC_OFFLINE_INTEGRATION_SRC}")

ADD_EXECUTABLE(pcl_offline_integration ${OI_SRCS} ${OI_INCS})
target_link_libraries(pcl_offline_integration ${PCL_LIBRARIES} ${OPENGL_LIBRARIES} ${QT_LIBRARIES})
