cmake_minimum_required(VERSION 3.16.3)
project(mwe)

include(ExternalProject)

ExternalProject_Add(LAPACK
  PREFIX 3rd_party
  URL https://github.com/Reference-LAPACK/lapack/archive/refs/tags/v3.10.1.tar.gz
  CONFIGURE_COMMAND mkdir -p build && cd build && cmake .. -DCMAKE_POSITION_INDEPENDENT_CODE=ON
  BUILD_COMMAND cd build && make -j
  BUILD_IN_SOURCE TRUE
  INSTALL_COMMAND ""
)

ExternalProject_Add(IPOPT
  PREFIX 3rd_party
  URL https://github.com/coin-or/Ipopt/archive/refs/heads/stable/3.14.zip
  CONFIGURE_COMMAND ./configure --with-lapack-lflags="-L${CMAKE_CURRENT_BINARY_DIR}/3rd_party/src/LAPACK/build/lib -llapack -lblas -lm -lgfortran" --with-pardiso=
  BUILD_COMMAND make -j
  BUILD_IN_SOURCE TRUE
  INSTALL_COMMAND ""
)

add_executable(main
  src/main.cpp
  src/MyNLP.cpp
)

add_dependencies(IPOPT LAPACK)
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
