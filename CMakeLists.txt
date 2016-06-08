project(f3_blink C ASM)
cmake_minimum_required(VERSION 3.5.0)

file(GLOB_RECURSE USER_SOURCES "Src/*.c")
file(GLOB_RECURSE HAL_SOURCES "Drivers/STM32F3xx_HAL_Driver/Src/*.c")

add_library(HAL ${HAL_SOURCES})
add_library(CMSIS
        Drivers/CMSIS/Device/ST/STM32F3xx/Source/Templates/system_stm32f3xx.c
        Drivers/CMSIS/Device/ST/STM32F3xx/Source/Templates/gcc/startup_stm32f303xe.s)

include_directories(Inc)
include_directories(Drivers/STM32F3xx_HAL_Driver/Inc)
include_directories(Drivers/CMSIS/Include)
include_directories(Drivers/CMSIS/Device/ST/STM32F3xx/Include)

add_definitions(-DSTM32F303xE)
add_executable(${PROJECT_NAME}.elf ${USER_SOURCES} ${LINKER_SCRIPT})

target_link_libraries(${PROJECT_NAME}.elf HAL CMSIS)

set(HEX_FILE ${PROJECT_SOURCE_DIR}/build/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_SOURCE_DIR}/build/${PROJECT_NAME}.bin)
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE} \nBuilding ${BIN_FILE}")
