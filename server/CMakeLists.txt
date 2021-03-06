file(GLOB_RECURSE SOURCE_FILES src/*.cpp)
file(GLOB_RECURSE LOCAL_HEADER_FILES hdr/*.*)
file(GLOB_RECURSE CONFIG_FILES config/*.*)

set(HEADER_FILES
	${HEADER_FILES}
	${LOCAL_HEADER_FILES}
	PARENT_SCOPE)

add_executable(server ${SOURCE_FILES} ${PLATFORM_SOURCE_FILES} ${LOCAL_HEADER_FILES} ${HEADER_FILES})
target_include_directories(server PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>")
set_property(TARGET server PROPERTY CXX_STANDARD 20)
set_property(TARGET server PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
set_property(TARGET server PROPERTY CXX_VISIBILITY_PRESET hidden)
set_property(TARGET server PROPERTY SOVERSION ${PROJECT_VERSION_MAJOR})
set_property(TARGET server PROPERTY VERSION ${PROJECT_VERSION})

# dependencies
target_link_libraries(server PUBLIC Threads::Threads)
if (WIN32)
	set(Boost_USE_STATIC_LIBS   ON)
	set(Boost_USE_STATIC_RUNTIME ON)
endif()
target_link_libraries(server PRIVATE Boost::system)
target_link_libraries(server PRIVATE OpenSSL::SSL)
target_link_libraries(server PRIVATE ZLIB::ZLIB)

install(FILES ${CONFIG_FILES} DESTINATION "${CONFIG_DIR}")


