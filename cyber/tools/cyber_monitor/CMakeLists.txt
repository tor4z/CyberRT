file(GLOB SRCS *.cc)

find_package(PkgConfig REQUIRED)
find_package(glog REQUIRED)
find_package(Protobuf REQUIRED)
pkg_check_modules(UUID REQUIRED uuid)

add_executable(cyber_monitor
    ${SRCS}
)

target_link_libraries(cyber_monitor
    ${PROJECT_NAME}
    ${Protobuf_LIBRARIES}
    glog::glog
    ncurses
    uuid
)
