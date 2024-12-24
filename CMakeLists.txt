cmake_minimum_required(VERSION 3.27)

project("Eao_Detail_ztd_text" LANGUAGES CXX VERSION 0.0.1 DESCRIPTION "Eao_Detail_ztd_text")

option(EAO_ZTD_TEXT_STANDALONE "Standalone and doesn't need Eao" OFF)

if(PROJECT_IS_TOP_LEVEL)
  if(EAO_ZTD_TEXT_STANDALONE)
    message(INFO "EAO_ZTD_TEXT Standalone mode: ON")
    set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_MODULE_PATH})
  else()
    if(NOT EXISTS ${EAO_CMAKE_MODULE_PATH})
      message(FATAL_ERROR "Invalid EAO_CMAKE_MODULE_PATH: \"${EAO_CMAKE_MODULE_PATH}\"")
    else()
      set(CMAKE_MODULE_PATH ${EAO_CMAKE_MODULE_PATH} ${CMAKE_MODULE_PATH})
      include(EaoCommon)
      eao_cmake_policies()
    endif()
  endif()
endif()


set(CMAKE_POLICY_DEFAULT_CMP0135 NEW)
set(CMAKE_POLICY_DEFAULT_CMP0048 NEW)
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)

# Dependencies
include(CPMPackageManager OPTIONAL RESULT_VARIABLE CPM_PRESENT)

if(NOT DEFINED ZTD_CMAKE_PROJECT_PRELUDE)
  CPMAddPackage(
    GITHUB_REPOSITORY frkami123/cmake
    GIT_SHALLOW    ON
    GIT_TAG "main"
    SYSTEM YES
    EXCLUDE_FROM_ALL YES
  )
endif()

if(NOT DEFINED ZTD_CMAKE_PROJECT_PRELUDE)
  message(FATAL_ERROR "something")
endif()
if(NOT TARGET ztd.idk)
    CPMAddPackage(
      GITHUB_REPOSITORY frkami123/idk
      GIT_SHALLOW    ON
      GIT_TAG "main"
      SYSTEM YES
      EXCLUDE_FROM_ALL YES
    )
endif()
if(NOT TARGET ztd.idk)
  message(FATAL_ERROR "ztd.idk is required")
endif()

if(NOT TARGET ztd.encoding_tables)
    CPMAddPackage(
      GITHUB_REPOSITORY frkami123/encoding_tables
      GIT_SHALLOW    ON
      GIT_TAG "main"
      SYSTEM YES
      EXCLUDE_FROM_ALL YES
    )
endif()
if(NOT TARGET ztd.encoding_tables)
  message(FATAL_ERROR "ztd.encoding_tables is required")
endif()

if(NOT TARGET ztd.platform)
    CPMAddPackage(
      GITHUB_REPOSITORY frkami123/platform
      GIT_SHALLOW    ON
      GIT_TAG "main"
      SYSTEM YES
      EXCLUDE_FROM_ALL YES
    )
endif()
if(NOT TARGET ztd.platform)
  message(FATAL_ERROR "ztd.platform is required")
endif()


if(NOT TARGET ztd.static_containers)
    CPMAddPackage(
      GITHUB_REPOSITORY frkami123/static_containers
      GIT_SHALLOW    ON
      GIT_TAG "main"
      SYSTEM YES
      EXCLUDE_FROM_ALL YES
    )
endif()
if(NOT TARGET ztd.static_containers)
  message(FATAL_ERROR "ztd.static_containers is required")
endif()
  
if(NOT TARGET ztd.cuneicode)
  CPMAddPackage(
    GITHUB_REPOSITORY frkami123/cuneicode
    GIT_SHALLOW    ON
    GIT_TAG "main"
    SYSTEM YES
    EXCLUDE_FROM_ALL YES
  )
endif()
if(NOT TARGET ztd.cuneicode)
  message(FATAL_ERROR "ztd.cuneicode is required")
endif()
  
CPMAddPackage(
  GITHUB_REPOSITORY frkami123/text
  GIT_SHALLOW    ON
  GIT_TAG "main"
  SYSTEM YES
  EXCLUDE_FROM_ALL YES
)

if(NOT EAO_ZTD_TEXT_STANDALONE)
  include(ProjectOptions)
else()
  macro(setup_project_options projectName targetName)
    set_property(TARGET ${targetName} PROPERTY CXX_STANDARD 23)
    target_compile_features(${targetName} INTERFACE cxx_std_23)
    set_property(TARGET ${targetName} PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)
    if (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
      target_compile_options(${targetName}
        INTERFACE
        /nologo
        /utf-8
        /permissive-
        /Zc:throwingNew-
        /Zc:preprocessor
        /Zc:externConstexpr
      )
    endif()
  endmacro()
endif()

setup_project_options(${PROJECT_NAME} ztd.version)
setup_project_options(${PROJECT_NAME} ztd.platform)
setup_project_options(${PROJECT_NAME} ztd.tag_invoke)
setup_project_options(${PROJECT_NAME} ztd.idk)
setup_project_options(${PROJECT_NAME} ztd.static_containers)
setup_project_options(${PROJECT_NAME} ztd.encoding_tables)
setup_project_options(${PROJECT_NAME} ztd.cuneicode)
setup_project_options(${PROJECT_NAME} ztd.text)

add_library(Eao_Detail_ztd_text STATIC dummy.cpp)
add_library(Eao::Detail::ztd::text ALIAS Eao_Detail_ztd_text)
setup_project_options(${PROJECT_NAME} Eao_Detail_ztd_text)
target_link_libraries(Eao_Detail_ztd_text PUBLIC ztd::text)

target_include_directories(
  Eao_Detail_ztd_text
  INTERFACE
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
)
