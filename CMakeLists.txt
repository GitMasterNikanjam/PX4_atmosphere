cmake_minimum_required(VERSION 3.16)

project(PX4_atmosphere LANGUAGES CXX)

add_library(atmosphere atmosphere.cpp)
add_library(PX4::atmosphere ALIAS atmosphere)
target_compile_features(atmosphere PUBLIC cxx_std_11)
target_include_directories(atmosphere PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
