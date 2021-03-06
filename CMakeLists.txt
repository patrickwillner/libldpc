cmake_minimum_required(VERSION 3.10)

# set the project name
project(LDPC-Simulator)

enable_testing()

set(CMAKE_CXX_FLAGS "-O3 -fopenmp -Wall -pthread")
set(SIM_FLAGS "" CACHE STRING "Compile flags (see flags.txt)")
set(BASE_SRC "src/core/gf2.cpp" "src/core/functions.cpp" "src/core/ldpc.cpp" "src/decoding/decoder.cpp" "src/sim/channel.cpp" "src/sim/ldpcsim.cpp")

# add the executable
add_executable(ldpcsim "src/sim_cpu.cpp" ${BASE_SRC})
target_compile_definitions(ldpcsim PRIVATE LOG_FRAME_TIME=1 ${SIM_FLAGS})

# add the executable
add_executable(ldpctest "tests/init.cpp" ${BASE_SRC})
target_compile_definitions(ldpctest PRIVATE ${SIM_FLAGS})

# shared library
add_library(ldpc SHARED "src/shared.cpp" ${BASE_SRC})
target_compile_definitions(ldpc PRIVATE LOG_FRAME_TIME=1 LIB_SHARED=1 ${SIM_FLAGS})

# specify the C++ standard
target_compile_features(ldpcsim PRIVATE cxx_std_17)
target_compile_features(ldpctest PRIVATE cxx_std_17)
target_compile_features(ldpc PRIVATE cxx_std_17)

add_test(NAME ldpctest COMMAND ldpctest ./tests/code/h.txt -G ./tests/code/g.txt)
