cmake_minimum_required(VERSION 3.2)

include_directories(
  ..
  ${EIGEN3_ROOT}/include/eigen3
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DNOCUDA -Wall -march=native -funroll-loops -g -D__STDC_FORMAT_MACROS")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DEIGEN_DONT_PARALLELIZE -O3")

if(CMAKE_HOST_APPLE OR APPLE)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DNORT")
endif()

if(NOTHREAD)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -DNOTHREAD")
elseif()
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -pthread")
endif()

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lm -lstdc++")

add_library(faster-rnnlm SHARED
  layers/simple_layer.cc
  layers/gru_layer.cc
  layers/scrn_layer.cc
  layers/layer_stack.cc
  layers/interface.cc
  hierarchical_softmax.cc
  nce.cc
  words.cc
  maxent.cc
  nnet.cc

  cuda_softmax.h          program_options.h
  hierarchical_softmax.h  settings.h
  maxent.h                util.h
  nce.h                   words.h
  nnet.h
)

add_executable(rnnlm
  rnnlm.cc
)

target_link_libraries(rnnlm
  faster-rnnlm
)

install_files(/include "\.h")
install(FILES ../LICENSE DESTINATION .)

INSTALL(TARGETS rnnlm faster-rnnlm 
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)
