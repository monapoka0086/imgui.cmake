cmake_minimum_required(VERSION 3.15)

project(imgui.cmake LANGUAGES CXX)

if (MSVC)
    add_compile_options("/MT")
    foreach(flag_var
        CMAKE_C_FLAGS CMAKE_C_FLAGS_DEBUG CMAKE_C_FLAGS_RELEASE
        CMAKE_C_FLAGS_MINSIZEREL CMAKE_C_FLAGS_RELWITHDEBINFO)
      if(${flag_var} MATCHES "/MD")
        string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
      endif()
    endforeach()
endif()


add_library(${PROJECT_NAME} STATIC)
target_sources(
    ${PROJECT_NAME}
    PRIVATE imgui/imgui.cpp imgui/imgui_demo.cpp imgui/imgui_draw.cpp
            imgui/imgui_widgets.cpp imgui/imgui_tables.cpp)

include(GNUInstallDirs)

target_include_directories(
    ${PROJECT_NAME} PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/imgui>
                           $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
target_compile_definitions(
    ${PROJECT_NAME}
    PUBLIC
        IMGUI_DISABLE_INCLUDE_IMCONFIG_H
        $<$<BOOL:${IMGUI_DISABLE_OBSOLETE_FUNCTIONS}>:IMGUI_DISABLE_OBSOLETE_FUNCTIONS>
)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_11)

set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX "d")

install(
    TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}-config
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(
    EXPORT ${PROJECT_NAME}-config
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})
install(FILES imgui/imgui.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
