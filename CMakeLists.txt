cmake_minimum_required(VERSION 3.2)

set(PROJECT_NAME dpdk-stack)
set(RELEASE_DATE 2017-12-04)
set(PROJECT_VERSION "0.0.1")
set(PROJECT_DESC "the native tcp/ip stack by dpdk")
project(${PROJECT_NAME})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(DPDK_DIR ${CMAKE_SOURCE_DIR}/dpdk)
set(DPDK_DECONFIG x86_64-native-linuxapp-gcc)
set(DPDK_BASE_LINK_PATH ${CMAKE_BINARY_DIR}/${DPDK_DECONFIG})

set(DPDK_STATIC_LIBS ${DPDK_BASE_LINK_PATH}/lib/librte_eal.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_bus_pci.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_cfgfile.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_cmdline.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_cryptodev.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_ethdev.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_eventdev.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_kni.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_kvargs.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_mbuf.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_member.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_mempool.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_mempool_octeontx.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_mempool_ring.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_mempool_stack.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_net.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_pci.a
                ${DPDK_BASE_LINK_PATH}/lib/librte_pmd_e1000.a
		${DPDK_BASE_LINK_PATH}/lib/librte_pmd_ixgbe.a
		${DPDK_BASE_LINK_PATH}/lib/librte_port.a
		${DPDK_BASE_LINK_PATH}/lib/librte_power.a
		${DPDK_BASE_LINK_PATH}/lib/librte_reorder.a
		${DPDK_BASE_LINK_PATH}/lib/librte_ring.a
		${DPDK_BASE_LINK_PATH}/lib/librte_sched.a
		${DPDK_BASE_LINK_PATH}/lib/librte_security.a
		${DPDK_BASE_LINK_PATH}/lib/librte_table.a
		${DPDK_BASE_LINK_PATH}/lib/librte_timer.a
		${DPDK_BASE_LINK_PATH}/lib/librte_vhost.a)

# Tools
set(CMAKE_MAKE make)
set(CMAKE_LN   ln)
set(CMAKE_ECHO echo)

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
endif ()

message(STATUS "Running cmake version ${CMAKE_VERSION}")

# Run platform tests
include(${CMAKE_SOURCE_DIR}/cmake/configure.cmake)
configure_file(${CMAKE_SOURCE_DIR}/cmake/config.h.cmake ${CMAKE_BINARY_DIR}/src/config.h)
add_definitions(-I$(CMAKE_BINARY_DIR)/src)
add_definitions(-DHAVE_CONFIG_H)

# Configuration DPDK
if (UNIX)
  if (CMAKE_SYSTEM_PROCESSOR MATCHES "amd64.*|x86_64.*|AMD64.*")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "i686.*|i386.*|x86.*|amd64.*|AMD64.*")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "^(arm.*|ARM.*)")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "^(aarch64.*|AARCH64.*)")
  elseif (CMAKE_SYSTEM_PROCESSOR MATCHES "^ppc64le.*|PPC64LE.*")
  else ()
  endif ()
else ()
endif ()
message(STATUS ${DPDK_DECONFIG})

add_custom_target(dpdk-dep ${CMAKE_ECHO} ${DPDK_DECONFIG} &&
			   ${CMAKE_MAKE} install T=${DPDK_DECONFIG} &&
			   ${CMAKE_LN} -f -s ${DPDK_DIR}/${DPDK_DECONFIG} ${CMAKE_BINARY_DIR}
                   WORKING_DIRECTORY ${DPDK_DIR}
                   COMMENT Build dpdk dep
                   )

add_subdirectory(src)
