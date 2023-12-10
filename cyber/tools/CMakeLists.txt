add_subdirectory(cyber_monitor)
add_subdirectory(cyber_recorder)
add_subdirectory(cyber_channel)
add_subdirectory(cyber_node)
add_subdirectory(cyber_service)

install(FILES cyber_tools_auto_complete.bash
    DESTINATION .
)
