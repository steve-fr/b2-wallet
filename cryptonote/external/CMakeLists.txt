set(UPNPC_BUILD_STATIC ON CACHE BOOL "Build static library")
set(UPNPC_BUILD_SHARED OFF CACHE BOOL "Build shared library")
set(UPNPC_BUILD_TESTS OFF CACHE BOOL "Build test executables")
set(WITH_MD_LIBRARY OFF CACHE BOOL "Code Generation for static library")

add_subdirectory(miniupnpc)

if (DO_TESTS)
  add_subdirectory(gtest)
endif()

if(MSVC)
    include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR} ../version)
    add_subdirectory(rocksDB EXCLUDE_FROM_ALL)
    set_property(TARGET upnpc-static rocksdb PROPERTY FOLDER "external")
elseif(NOT MSVC)
    add_subdirectory(rocksdb EXCLUDE_FROM_ALL)
    set_property(TARGET upnpc-static rocksdb PROPERTY FOLDER "external")
    set_target_properties(rocksdb PROPERTIES IMPORTED_LOCATION ${CMAKE_CURRENT_SOURCE_DIR}/rocksdb/librocksdb.a)
endif()

if(DO_TESTS)
  set_property(TARGET gtest gtest_main PROPERTY FOLDER "external")
endif()

if(MSVC)
  set_property(TARGET upnpc-static APPEND_STRING PROPERTY COMPILE_FLAGS " -wd4244 -wd4267")
elseif(NOT MSVC)
  set_property(TARGET upnpc-static APPEND_STRING PROPERTY COMPILE_FLAGS " -Wno-undef -Wno-unused-result -Wno-unused-value")
endif()

