set(TARGET ${PROJECT_NAME})

enable_language(ASM)

find_package(Protobuf REQUIRED)
find_package(fastrtps 1.5.0 REQUIRED)
find_package(fastcdr 1.0.7 REQUIRED)
find_package(nlohmann_json REQUIRED)

file(GLOB_RECURSE SRCS *.cc)
list(FILTER SRCS EXCLUDE REGEX "tools/.*")
list(FILTER SRCS EXCLUDE REGEX "python/.*")
list(FILTER SRCS EXCLUDE REGEX "examples/.*")
list(FILTER SRCS EXCLUDE REGEX "example/.*")
list(FILTER SRCS EXCLUDE REGEX "mainboard/.*")
list(FILTER SRCS EXCLUDE REGEX ".*_test.cc")
list(FILTER SRCS EXCLUDE REGEX ".*/test/.*")


set(CROUTINE_FILE "croutine/detail/swap_x86_64.S")
if(${CMAKE_SYSTEM_PROCESSOR} STREQUAL "x86_64")
  set(CROUTINE_FILE "croutine/detail/swap_x86_64.S")
else()
  set(CROUTINE_FILE "croutine/detail/swap_aarch64.S")
endif(${CMAKE_SYSTEM_PROCESSOR} STREQUAL "x86_64")


include_directories(${Protobuf_INCLUDE_DIRS}
                    ${fastcdr_INCLUDE_DIR}
                    ${fastrtps_INCLUDE_DIR}
                    ${NlohmannJson_INCLUDE_DIRS})

link_directories(${fastcdr_LIB_DIR}
                 ${fastrtps_LIB_DIR})

add_library(${TARGET} SHARED
    ${SRCS}
    ${MODULES_SRCS}
    ${CROUTINE_FILE}
)

target_link_libraries(${TARGET}
    PUBLIC
        ${Protobuf_LIBRARIES}
        fastrtps
        fastcdr
        nlohmann_json::nlohmann_json
        atomic
)


add_subdirectory(tools)


install(DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
    DESTINATION include
    FILES_MATCHING
    PATTERN *.h
)


configure_file(setup.bash.in ${CMAKE_BINARY_DIR}/setup.bash @ONLY)


install(DIRECTORY conf
    DESTINATION .
)


install(TARGETS ${TARGET}
    LIBRARY
    DESTINATION lib
)

install(FILES ${CMAKE_BINARY_DIR}/setup.bash
    DESTINATION .
)


install(TARGETS ${TARGET}
    EXPORT ${TARGET}Targets
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(EXPORT ${TARGET}Targets
    DESTINATION ${CMAKE_INSTALL_PREFIX}/cmake/${PROJECT_NAME}
)
