cmake_minimum_required(VERSION 3.15.0)

project(cyber VERSION 8.0.0)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}/cyber)

file(GLOB CYBER_PROTO_SRCS cyber/proto/*.proto)
file(GLOB_RECURSE MODULES_PROTO_SRCS modules/common_msgs/*.proto)
list(APPEND CYBER_PROTO_SRCS ${MODULES_PROTO_SRCS})

foreach(proto ${CYBER_PROTO_SRCS})
    execute_process(
        COMMAND ${Protobuf_PROTOC_EXECUTABLE}
        -I${CMAKE_CURRENT_SOURCE_DIR}
        --cpp_out=${CMAKE_CURRENT_SOURCE_DIR}
        --python_out=${CMAKE_CURRENT_SOURCE_DIR}
        ${proto})
endforeach(proto ${CYBER_PROTO_SRCS})

file(GLOB_RECURSE MODULES_SRCS modules/common_msgs/*.cc)


include_directories(${CMAKE_CURRENT_LIST_DIR})

add_subdirectory(cyber)


include(CMakePackageConfigHelpers)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/${PROJECT_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_PREFIX}/cmake/${PROJECT_NAME}
)

write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/cyberConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
)


install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/cyberConfigVersion.cmake
    DESTINATION cmake/${PROJECT_NAME}
)
