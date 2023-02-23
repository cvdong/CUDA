cmake_minimum_required(VERSION 2.6)
project(pro)
add_definitions(-std=c++11)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Release)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/workspace)

set(CUDA_GEN_CODE "-gencode=arch=compute_86,code=sm_86")

set(CUDA_DIR     "/usr/local/cuda-11.7")
set(TENSORRT_DIR "/home/Downloads/TensorRT-8.4.1.5")

find_package(CUDA REQUIRED)
find_package(OpenCV)

include_directories(
    ${PROJECT_SOURCE_DIR}/src
    ${OpenCV_INCLUDE_DIRS}
    ${CUDA_DIR}/include
    ${TENSORRT_DIR}/include
)

link_directories(
    ${TENSORRT_DIR}/lib
    ${CUDA_DIR}/lib64
)

set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -O0 -Wfatal-errors -pthread -w -g")
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -O0 -Xcompiler -fPIC -g -w ${CUDA_GEN_CODE}")
file(GLOB_RECURSE cpp_srcs ${PROJECT_SOURCE_DIR}/src/*.cpp)
file(GLOB_RECURSE cuda_srcs ${PROJECT_SOURCE_DIR}/src/*.cu)

add_executable(pro ${cpp_srcs})

target_link_libraries(pro nvinfer)
target_link_libraries(pro cudart)
target_link_libraries(pro ${OpenCV_LIBS})


add_custom_target(
    run
    DEPENDS pro
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/workspace
    COMMAND ./pro
)