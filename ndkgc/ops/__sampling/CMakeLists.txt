cmake_minimum_required(VERSION 3.0)
# if libraries are compiled with different ABI, then set the matching version here
project(target_sampling)

set(GCC_COVERAGE_LINK_FLAGS "python3 -c 'import tensorflow as tf; print(" ".join(tf.sysconfig.get_link_flags()))'")

set(CMAKE_CXX_FLAGS "-D_GLIBCXX_USE_CXX11_ABI=0" CACHE STRING "" FORCE)  # Added force set the cxx compiler flag

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -shared -fPIC -O2")

set(CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} ${GCC_COVERAGE_LINK_FLAGS}")


set(CMAKE_CXX_STANDARD 11)



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
