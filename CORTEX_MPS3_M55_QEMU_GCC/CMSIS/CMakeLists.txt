# Copyright 2023 Arm Limited and/or its affiliates <open-source-office@arm.com>
#
# SPDX-License-Identifier: MIT

cmake_minimum_required(VERSION 3.15)

add_library(cmsis_bsp INTERFACE)

target_include_directories(cmsis_bsp
INTERFACE
    include
)

target_compile_definitions(cmsis_bsp
INTERFACE
    ARMCM55
)

target_sources(cmsis_bsp
INTERFACE
    ${CMAKE_CURRENT_SOURCE_DIR}/source/startup_ARMCM55.c
    ${CMAKE_CURRENT_SOURCE_DIR}/source/system_ARMCM55.c
)
