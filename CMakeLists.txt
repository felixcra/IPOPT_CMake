cmake_minimum_required(VERSION 3.16.3)
project(mwe)

include(ExternalProject)

#--- Configuration ---#
set(USE_INTEL_MKL TRUE)

if(USE_INTEL_MKL)
else()
  message(FATAL_ERROR "Currently, only USE_INTEL_MKL==TRUE is supported.")
endif()

ExternalProject_Add(IPOPT
  PREFIX 3rd_party
  URL https://github.com/felixcra/Ipopt/archive/refs/heads/stable/3.14.zip
  CONFIGURE_COMMAND ./configure CC=icx CXX=icpx
  BUILD_COMMAND make -j
  BUILD_IN_SOURCE TRUE
  INSTALL_COMMAND ""
)

add_executable(main
  src/main.cpp
  src/MyNLP.cpp
)

add_dependencies(main IPOPT)

target_include_directories(main
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/3rd_party/src/IPOPT/src/Interfaces
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/3rd_party/src/IPOPT/src/Common
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/3rd_party/src/IPOPT/src/LinAlg
  PRIVATE src
)

target_link_libraries(main
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/3rd_party/src/IPOPT/src/.libs/libipopt.so
)
