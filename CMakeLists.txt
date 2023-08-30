cmake_minimum_required(VERSION 3.5)
project(profiledpid_controller)

# Default to C99
if (NOT CMAKE_C_STANDARD)
    set(CMAKE_C_STANDARD 99)
endif ()

# Default to C++14
if (NOT CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 14)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif ()

if (CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(-Wall -Wextra -Wpedantic)
endif ()

# spdlog
find_package(spdlog REQUIRED)

add_library(${PROJECT_NAME} SHARED
        src/TrapezoidProfile.cpp
        src/PIDController.cpp)

target_include_directories(${PROJECT_NAME} PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include)


set(CMAKE_BUILD_TYPE Debug)

# CAN driver test files
add_executable(Tester test/tester.cpp)

target_include_directories(Tester PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_link_libraries(Tester ${PROJECT_NAME})

set(HEADER_FILES
        ${CMAKE_CURRENT_SOURCE_DIR}/include/PIDController.h
        ${CMAKE_CURRENT_SOURCE_DIR}/include/ProfiledPIDController.h
        ${CMAKE_CURRENT_SOURCE_DIR}/include/TrapezoidProfile.h
)

set_target_properties(${PROJECT_NAME}
        PROPERTIES
        FRAMEWORK ON
        SOVERSION 0
        VERSION 0.1.0
        PUBLIC_HEADER "${HEADER_FILES}"
        # PRIVATE_HEADER "${PRIVATE_HEADER_FILES}"
        LIBRARY_OUTPUT_DIRECTORY "lib"
        OUTPUT_NAME ${PROJECT_NAME}
)

# set(CMAKE_INSTALL_PREFIX ${CMAKE_CURRENT_SOURCE_DIR}/install)

install(TARGETS ${PROJECT_NAME}
        LIBRARY DESTINATION lib
        PUBLIC_HEADER DESTINATION include
)
