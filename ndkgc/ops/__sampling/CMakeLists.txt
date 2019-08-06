cmake_minimum_required(VERSION 3.0)
# if libraries are compiled with different ABI, then set the matching version here
if(CONAN_LIBCXX STREQUAL "libstdc++11")
    add_definitions(-D_GLIBCXX_USE_CXX11_ABI=0)
elseif(CONAN_LIBCXX STREQUAL "libstdc++")
    add_definitions(-D_GLIBCXX_USE_CXX11_ABI=1)
endif()

macro(use_cxx11)
  if (CMAKE_VERSION VERSION_LESS "3.1")
    if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
      set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
    endif ()
  else ()
    set (CMAKE_CXX_STANDARD 11)
  endif ()
endmacro(use_cxx11)

use_cxx11()

project(target_sampling)

EXEC_PROGRAM(uname OUTPUT_VARIABLE OS)
MESSAGE(STATUS "OS Type: " ${OS})




if (${OS} STREQUAL "Darwin")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -undefined dynamic_lookup")
endif ()


EXEC_PROGRAM("python3 -c 'import tensorflow as tf; print(tf.sysconfig.get_include())' 2>/dev/null | grep -v 'I'" OUTPUT_VARIABLE TF_FILES ERROR_VARIABLE TF_ERRORS)
message(STATUS "TensorFlow include dir: " ${TF_FILES})

set(SOURCE_FILES pairwise_sampler.cpp)

include_directories(${TF_FILES})

add_library(sampling SHARED ${SOURCE_FILES})
