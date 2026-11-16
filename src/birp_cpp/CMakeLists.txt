cmake_minimum_required(VERSION 3.14)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Policies (for properly setting the timestamps of extracted contents)
if (POLICY CMP0048)
    cmake_policy(SET CMP0048 NEW)
endif (POLICY CMP0048)
if (POLICY CMP0135)
    cmake_policy(SET CMP0135 NEW)
endif (POLICY CMP0135)

# Project
project(birp LANGUAGES CXX)

# Download coretools and stattools
include(FetchContent)
FetchContent_Declare(coretools
        URL https://bitbucket.org/wegmannlab/coretools/get/6682af5bb0ce424a4d80441a3cd57d2384adc88b.tar.gz
        SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/coretools")

FetchContent_Declare(stattools
        URL https://bitbucket.org/wegmannlab/stattools/get/e8c455f3c540b1d4b1d2620c9545cce23089468d.tar.gz
        SOURCE_DIR  "${CMAKE_CURRENT_SOURCE_DIR}/stattools")

FetchContent_MakeAvailable(coretools stattools)

#######################
# Create core library
#######################

# assemble list of cpp and h files in core folder
file(GLOB_RECURSE CORE_SOURCES core/*.h core/*.cpp)

#Core library
add_library(${PROJECT_NAME}Lib STATIC ${CORE_SOURCES})

target_sources(${PROJECT_NAME}Lib PRIVATE ${CORE_SOURCES})
target_include_directories(${PROJECT_NAME}Lib PRIVATE core SYSTEM INTERFACE core)
target_link_libraries(${PROJECT_NAME}Lib PUBLIC coretools stattools)
target_compile_features(${PROJECT_NAME}Lib PUBLIC cxx_std_17)
set_target_properties(${PROJECT_NAME}Lib PROPERTIES CXX_EXTENSIONS OFF)

if(NOT RCPP)
  target_compile_options(${PROJECT_NAME}Lib PRIVATE -Wall -Wextra)

  #######################
  # Executable
  #######################

  # add executable to be built
  add_executable(${PROJECT_NAME} main.cpp)

  # link libraries
  target_link_libraries(${PROJECT_NAME} PUBLIC ${PROJECT_NAME}Lib)
  target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)
  target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra)
endif()

