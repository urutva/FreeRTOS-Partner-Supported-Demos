# Copyright 2023 Arm Limited and/or its affiliates <open-source-office@arm.com>
#
# SPDX-License-Identifier: MIT

cmake_minimum_required(VERSION 3.15)

project(
	example
	VERSION 0.1
	LANGUAGES C ASM)

set(CMAKE_BUILD_TYPE Debug)

get_filename_component(FREERTOS_DIR_PATH ${CMAKE_CURRENT_LIST_DIR}/../../../.. REALPATH)

set(KERNEL_DIR_PATH ${FREERTOS_DIR_PATH}/Source)
set(DEMO_COMMON_PATH ${FREERTOS_DIR_PATH}/Demo/Common)

add_executable(cortex_mps3_m55_qemu_example)

target_sources(cortex_mps3_m55_qemu_example
	PRIVATE
	${CMAKE_CURRENT_SOURCE_DIR}/main.c
	${CMAKE_CURRENT_SOURCE_DIR}/syscall.c
)

if(DEFINED BLINKY_DEMO)
	message(DEBUG "BLINKY_DEMO variable set to ${BLINKY_DEMO}, building blinky demo")
	target_sources(cortex_mps3_m55_qemu_example
		PRIVATE
		${CMAKE_CURRENT_SOURCE_DIR}/main_blinky.c
	)

	target_compile_definitions(cortex_mps3_m55_qemu_example
		PRIVATE
		mainCREATE_SIMPLE_BLINKY_DEMO_ONLY=1
	)
elseif(DEFINED FULL_DEMO)
	message(DEBUG "FULL_DEMO variable set to ${FULL_DEMO}, building full demo")
	target_sources(cortex_mps3_m55_qemu_example
		PRIVATE
		${CMAKE_CURRENT_SOURCE_DIR}/main_full.c
		${CMAKE_CURRENT_SOURCE_DIR}/syscall.c
		${KERNEL_DIR_PATH}/stream_buffer.c
		${DEMO_COMMON_PATH}/Minimal/AbortDelay.c
		${DEMO_COMMON_PATH}/Minimal/BlockQ.c
		${DEMO_COMMON_PATH}/Minimal/blocktim.c
		${DEMO_COMMON_PATH}/Minimal/countsem.c
		${DEMO_COMMON_PATH}/Minimal/death.c
		${DEMO_COMMON_PATH}/Minimal/dynamic.c
		${DEMO_COMMON_PATH}/Minimal/EventGroupsDemo.c
		${DEMO_COMMON_PATH}/Minimal/flop.c
		${DEMO_COMMON_PATH}/Minimal/GenQTest.c
		${DEMO_COMMON_PATH}/Minimal/integer.c
		${DEMO_COMMON_PATH}/Minimal/IntSemTest.c
		${DEMO_COMMON_PATH}/Minimal/MessageBufferAMP.c
		${DEMO_COMMON_PATH}/Minimal/MessageBufferDemo.c
		${DEMO_COMMON_PATH}/Minimal/PollQ.c
		${DEMO_COMMON_PATH}/Minimal/QPeek.c
		${DEMO_COMMON_PATH}/Minimal/QueueOverwrite.c
		${DEMO_COMMON_PATH}/Minimal/QueueSet.c
		${DEMO_COMMON_PATH}/Minimal/QueueSetPolling.c
		${DEMO_COMMON_PATH}/Minimal/recmutex.c
		${DEMO_COMMON_PATH}/Minimal/semtest.c
		${DEMO_COMMON_PATH}/Minimal/StaticAllocation.c
		${DEMO_COMMON_PATH}/Minimal/StreamBufferDemo.c
		${DEMO_COMMON_PATH}/Minimal/StreamBufferInterrupt.c
		${DEMO_COMMON_PATH}/Minimal/TaskNotify.c
		${DEMO_COMMON_PATH}/Minimal/TimerDemo.c
	)

	target_compile_definitions(cortex_mps3_m55_qemu_example
		PRIVATE
		mainCREATE_FULL_DEMO_ONLY=1
	)
	target_include_directories(cortex_mps3_m55_qemu_example
		PRIVATE
		${DEMO_COMMON_PATH}/include
	)
endif()

add_subdirectory(CMSIS)

target_link_libraries(cortex_mps3_m55_qemu_example
	PRIVATE
	cmsis_bsp
)

add_library(freertos_config INTERFACE)

target_include_directories(freertos_config SYSTEM
	INTERFACE
	config
)

target_compile_definitions(freertos_config
	INTERFACE
	projCOVERAGE_TEST=0
)

set(FREERTOS_HEAP "4" CACHE STRING "" FORCE)

# Select the native compile PORT
set(FREERTOS_PORT "GCC_ARM_CM55_NTZ_NONSECURE" CACHE STRING "" FORCE)

add_subdirectory(../../../../Source freertos_kernel)

target_link_options(cortex_mps3_m55_qemu_example PRIVATE -T ${CMAKE_CURRENT_SOURCE_DIR}/gcc_arm.ld)
target_link_options(cortex_mps3_m55_qemu_example PRIVATE "-Wl,-Map=$<TARGET_FILE_DIR:cortex_mps3_m55_qemu_example>/cortex_mps3_m55_qemu_example.map")

target_link_libraries(freertos_kernel_port
	PRIVATE
	cmsis_bsp
)

target_link_libraries(freertos_kernel
	PRIVATE
	cmsis_bsp
)

target_link_libraries(cortex_mps3_m55_qemu_example
	PRIVATE
	freertos_kernel
)

add_custom_command(
	TARGET cortex_mps3_m55_qemu_example
	POST_BUILD
	COMMAND arm-none-eabi-objcopy -O binary
	"$<TARGET_FILE:cortex_mps3_m55_qemu_example>" "$<TARGET_FILE_DIR:cortex_mps3_m55_qemu_example>/cortex_mps3_m55_qemu_example.bin"
	VERBATIM
)
