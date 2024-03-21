cmake_minimum_required(VERSION 3.13)

# initialize pico-sdk from submodule
# note: this must happen before project()
include(${CMAKE_CURRENT_SOURCE_DIR}/deps/pico-sdk/pico_sdk_init.cmake)

project(my_project CXX C ASM)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# rest of your project
if (TARGET tinyusb_device)
    add_executable(obc obc/main.c)

    # pull in common dependencies
    target_link_libraries(obc pico_stdlib)

    # enable usb output, disable uart output
    pico_enable_stdio_usb(obc 1)
    pico_enable_stdio_uart(obc 0)

    # create map/bin/hex/uf2 file etc.
    pico_add_extra_outputs(obc)
elseif(PICO_ON_DEVICE)
    message(WARNING "not building obc because TinyUSB submodule is not initialized in the SDK")
endif()
