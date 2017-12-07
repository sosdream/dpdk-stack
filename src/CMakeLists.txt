set(DPDK_STACK_SOURCE
	main.c
	)
#message(STATUS "libs ${DPDK_STATIC_LIBS}")
set(DPDK_STACK_EXTRA_LIBS pthread dl numa)
add_executable(dpdk_stack ${DPDK_STACK_SOURCE})
add_dependencies(dpdk_stack dpdk-dep)
include_directories(${DPDK_BASE_LINK_PATH}/include)
target_link_libraries(dpdk_stack ${DPDK_STATIC_LIBS} ${DPDK_STACK_EXTRA_LIBS})
target_compile_definitions(dpdk_stack PUBLIC -DMODULE_STACK)


# ------------------------------------------------------------------
# Misc
# Recommend to install shared by default
install(DIRECTORY ${RUNTIME_SHARED_OUTPUT_DIRECTORY}/
	DESTINATION bin)
