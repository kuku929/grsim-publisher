cmake_minimum_required(VERSION 3.5)

# we will append all libs to this var
set(libs)

# create the target before the sources list is known so that we can call
# add_dependencies(<target> external_proj)
set(app ${CMAKE_PROJECT_NAME})
add_executable(${app} "")

## Project branding, version and package mantainer
project(grsim-publisher)

#Qt
find_package(Qt5 COMPONENTS Core Widgets OpenGL Network REQUIRED)
list(APPEND libs Qt5::Core Qt5::Widgets Qt5::OpenGL Qt5::Network)

# Protobuf
find_package(Protobuf REQUIRED)
if(TARGET protobuf_external)
    add_dependencies(${app} protobuf_external)
endif()
set(protobuf_MODULE_COMPATIBLE ON CACHE BOOL "")
include_directories(${Protobuf_INCLUDE_DIRS})
list(APPEND libs ${Protobuf_LIBRARIES})
set (Protobuf_IMPORT_DIRS ${Protobuf_INCLUDE_DIR})

protobuf_generate_cpp(PROTO_CPP PROTO_H
    include/protobuf-files/pb/proto/grSim_Commands.proto
    include/protobuf-files/pb/proto/grSim_Packet.proto
    include/protobuf-files/pb/proto/grSim_Replacement.proto
    include/protobuf-files/pb/proto/grSim_Robotstatus.proto
    #include/protobuf-files/pb/proto/ssl_simulation_config.proto
    #include/protobuf-files/pb/proto/ssl_simulation_control.proto
    #include/protobuf-files/pb/proto/ssl_simulation_error.proto
    #include/protobuf-files/pb/proto/ssl_simulation_robot_control.proto
    #include/protobuf-files/pb/proto/ssl_simulation_robot_feedback.proto
    #include/protobuf-files/pb/proto/ssl_simulation_synchronous.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_detection.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_detection_tracked.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_geometry_legacy.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_geometry.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_refbox_log.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_wrapper_legacy.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_wrapper.proto
	include/protobuf-files/pb/proto/messages_robocup_ssl_wrapper_tracked.proto
)
# ssl-client
include_directories(./include)

set(SOURCES
    src/main.cpp
    include/net/robocup_ssl_server/robocup_ssl_server.cpp
    include/net/robocup_ssl_client/robocup_ssl_client.cpp
    include/net/netraw/netraw.cpp
)

set(HEADERS
    include/net/robocup_ssl_server/robocup_ssl_server.h
    include/net/robocup_ssl_client/robocup_ssl_client.h
)

# files to be compiled
set(srcs
    #${CONFIG_FILES}
    ${PROTO_CPP}
    ${PROTO_H}
    #${RESOURCES}
    ${HEADERS}
    ${SOURCES}
)



target_sources(${app} PRIVATE ${srcs})
install(TARGETS ${app} DESTINATION bin)
target_link_libraries(${app} ${libs})

