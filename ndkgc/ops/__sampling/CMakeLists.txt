cmake_minimum_required(VERSION 3.1)
# if libraries are compiled with different ABI, then set the matching version here


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -stdlib=libstdc++")

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
