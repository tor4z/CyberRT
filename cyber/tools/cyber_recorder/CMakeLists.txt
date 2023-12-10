set(TARGET cyber_recorder)

file(GLOB_RECURSE SRCS *.cc)

find_package(PkgConfig REQUIRED)
find_package(glog REQUIRED)
find_package(Protobuf REQUIRED)
pkg_check_modules(UUID REQUIRED uuid)

add_executable(${TARGET}
    ${SRCS}
)

target_link_libraries(${TARGET}
    ${PROJECT_NAME}
    ${Protobuf_LIBRARIES}
    glog::glog
    ncurses
    uuid
)


install(TARGETS ${TARGET}
    RUNTIME
    DESTINATION bin
)
