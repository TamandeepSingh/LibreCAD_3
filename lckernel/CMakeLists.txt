cmake_minimum_required(VERSION 2.8.11)
project(LCKERNEL)

add_definitions("-Wall")

#Include TinySpline
add_definitions("-DTINYSPLINE_DOUBLE_PRECISION")
add_subdirectory("tinyspline")

add_definitions(-std=c++14)

message("***** LibreCAD kernel *****")

set (CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# SOURCE FILES
set(lckernel_srcs
version.cpp
cad/base/cadentity.cpp
cad/base/id.cpp
cad/base/metainfo.cpp
cad/base/visitor.h
cad/dochelpers/documentimpl.cpp
cad/dochelpers/entitycontainer.cpp
cad/dochelpers/quadtree.cpp
cad/dochelpers/storagemanagerimpl.cpp
cad/dochelpers/undomanagerimpl.cpp
cad/document/document.cpp
cad/functions/intersect.cpp
cad/geometry/geoarc.cpp
cad/geometry/geocircle.cpp
cad/geometry/geocoordinate.cpp
cad/geometry/geoellipse.cpp
cad/geometry/geospline.cpp
cad/geometry/geobezier.cpp
cad/geometry/geobeziercubic.cpp
cad/math/lcmath.cpp
cad/math/equation.cpp
cad/math/intersectionhandler.cpp
cad/meta/layer.cpp
cad/meta/icolor.cpp
cad/meta/metalinewidth.cpp
cad/meta/dxflinepattern.cpp
cad/operations/builder.cpp
cad/operations/builderops.cpp
cad/operations/documentoperation.cpp
cad/operations/layerops.cpp
cad/operations/linepatternops.cpp
cad/primitive/arc.cpp
cad/primitive/circle.cpp
cad/primitive/point.cpp
cad/primitive/ellipse.cpp
cad/primitive/line.cpp
cad/primitive/spline.cpp
cad/primitive/text.cpp
cad/primitive/dimension.cpp
cad/primitive/dimradial.cpp
cad/primitive/dimdiametric.cpp
cad/primitive/dimlinear.cpp
cad/primitive/dimaligned.cpp
cad/primitive/dimangular.cpp
cad/primitive/lwpolyline.cpp
cad/primitive/beziercurve.cpp
cad/primitive/image.cpp
cad/math/helpermethods.cpp
)

# HEADER FILES
set(lckernel_hdrs
version.h
cad/const.h
cad/base/id.h
cad/base/cadentity.h
cad/base/metainfo.h
cad/dochelpers/documentimpl.h
cad/dochelpers/entitycontainer.h
cad/dochelpers/quadtree.h
cad/dochelpers/storagemanagerimpl.h
cad/dochelpers/undomanagerimpl.h
cad/document/document.h
cad/document/storagemanager.h
cad/document/undomanager.h
cad/events/addentityevent.h
cad/events/addlayerevent.h
cad/events/addlinepatternevent.h
cad/events/beginprocessevent.h
cad/events/commitprocessevent.h
cad/events/removeentityevent.h
cad/events/removelayerevent.h
cad/events/removelinepatternevent.h
cad/events/replaceentityevent.h
cad/events/replacelayerevent.h
cad/events/replacelinepatternevent.h
cad/functions/ftrim.h
cad/functions/intersect.h
cad/geometry/geobase.h
cad/geometry/geocoordinate.h
cad/geometry/geoarc.h
cad/geometry/geoarea.h
cad/geometry/geocircle.h
cad/geometry/geoellipse.h
cad/geometry/geospline.h
cad/geometry/geovector.h
cad/geometry/geobezierbase.h
cad/geometry/geobezier.h
cad/geometry/geobeziercubic.h
cad/interface/entitydispatch.h
cad/interface/metatype.h
cad/interface/snapable.h
cad/interface/snapconstrain.h
cad/math/lcmath.h
cad/math/equation.h
cad/math/intersectionhandler.h
cad/meta/color.h
cad/meta/icolor.h
cad/meta/metacolor.h
cad/meta/layer.h
cad/meta/metalinewidth.h
cad/meta/dxflinepattern.h
cad/operations/builder.h
cad/operations/builderops.h
cad/operations/documentoperation.h
cad/operations/trim.h
cad/operations/layerops.h
cad/operations/undoable.h
cad/operations/linepatternops.h
cad/vo/entitycoordinate.h
cad/vo/entitydistance.h
cad/primitive/arc.h
cad/primitive/circle.h
cad/primitive/point.h
cad/primitive/ellipse.h
cad/primitive/line.h
cad/primitive/spline.h
cad/primitive/text.h
cad/primitive/lwpolyline.h
cad/primitive/dimension.h
cad/primitive/dimradial.h
cad/primitive/dimdiametric.h
cad/primitive/dimlinear.h
cad/primitive/dimaligned.h
cad/primitive/dimangular.h
cad/primitive/textconst.h
cad/functions/string_helper.h
cad/primitive/beziercurve.h
cad/primitive/image.h
cad/math/helpermethods.h
version.h version.h)

# LOG4CXX
find_package(Log4CXX 1.10 REQUIRED)
include_directories(${LOG4CXX_INCLUDE_DIRS})
link_directories(${LOG4CXX_LIBRARY_DIRS})

# Eigen 3
find_package(Eigen3 REQUIRED)
if(CMAKE_COMPILER_IS_GNUCXX)
    include_directories(SYSTEM ${EIGEN3_INCLUDE_DIR})
else()
    include_directories(${EIGEN3_INCLUDE_DIR})
endif()

# BUILDING CONFIG
# SEPERATE BUILDING FLAG
set(SEPARATE_BUILD OFF)

if (SEPARATE_BUILD STREQUAL "ON")
    include_directories("${CMAKE_SOURCE_DIR}")
    include_directories("${CMAKE_SOURCE_DIR}/../third_party")
else()
    include_directories("${CMAKE_SOURCE_DIR}/lckernel")
    include_directories("${CMAKE_SOURCE_DIR}/third_party")
endif()

#Create version.cpp
add_custom_command(
        PRE_BUILD
        OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/version.cpp
        COMMAND ${CMAKE_COMMAND} -DDIR=${CMAKE_CURRENT_SOURCE_DIR} -P "${CMAKE_CURRENT_SOURCE_DIR}/cmake/SetBuildInfo.cmake"
        DEPENDS always_rebuild
)

# Anything that depends on this will always be rebuilt.
add_custom_command(
        OUTPUT always_rebuild
        COMMAND ${CMAKE_COMMAND} -E echo
)

add_library(lckernel SHARED ${lckernel_srcs} ${lckernel_hdrs})
target_link_libraries(lckernel ${LOG4CXX_LIBRARIES} ${APR_LIBRARIES} ${G_EXTRA_LIBS} tinysplinecpp_shared)

# INSTALLATION
install(TARGETS lckernel DESTINATION lib)
install(FILES ${kernel_hdrs}  DESTINATION include)

