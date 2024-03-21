cmake_minimum_required(VERSION 3.13)

# initialize pico-sdk from submodule
# note: this must happen before project()
include(${CMAKE_CURRENT_SOURCE_DIR}/deps/pico-sdk/pico_sdk_init.cmake)

project(my_project CXX C ASM)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# Include FreeRTOS
set(PICO_SDK_FREERTOS_SOURCE deps/FreeRTOS-Kernel)

add_library(freertos
    ${PICO_SDK_FREERTOS_SOURCE}/event_groups.c
    ${PICO_SDK_FREERTOS_SOURCE}/list.c
    ${PICO_SDK_FREERTOS_SOURCE}/queue.c
    ${PICO_SDK_FREERTOS_SOURCE}/stream_buffer.c
    ${PICO_SDK_FREERTOS_SOURCE}/tasks.c
    ${PICO_SDK_FREERTOS_SOURCE}/timers.c
    ${PICO_SDK_FREERTOS_SOURCE}/portable/MemMang/heap_3.c
    ${CMAKE_CURRENT_SOURCE_DIR}/freertos/port.c
)
target_include_directories(freertos PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/freertos
    ${PICO_SDK_FREERTOS_SOURCE}/include
    ${PICO_SDK_FREERTOS_SOURCE}/portable/GCC/ARM_CM0
)

# rest of your project
if (TARGET tinyusb_device)
    add_executable(obc obc/main.c)

    # pull in common dependencies
    target_link_libraries(obc pico_stdlib freertos)

    # enable usb output, disable uart output
    pico_enable_stdio_usb(obc 1)
    pico_enable_stdio_uart(obc 0)

    # create map/bin/hex/uf2 file etc.
    pico_add_extra_outputs(obc)
elseif(PICO_ON_DEVICE)
    message(WARNING "not building obc because TinyUSB submodule is not initialized in the SDK")
endif()



# rest of your project
if (TARGET tinyusb_device)
    add_executable(blink blink/blink.c)

    # pull in common dependencies
    target_link_libraries(blink pico_stdlib freertos)

    # enable usb output, disable uart output
    pico_enable_stdio_usb(blink 1)
    pico_enable_stdio_uart(blink 0)

    # create map/bin/hex/uf2 file etc.
    pico_add_extra_outputs(blink)
elseif(PICO_ON_DEVICE)
    message(WARNING "not building obc because TinyUSB submodule is not initialized in the SDK")
endif()
