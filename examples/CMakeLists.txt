cmake_minimum_required(VERSION 3.2)

set(CPU_FLAGS "--cpu Cortex-M3")

set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_ASM_COMPILER  "C:/Program Files (x86)/IAR Systems/Embedded Workbench 7.5/arm/bin/iasmarm.exe")
set(CMAKE_C_COMPILER    "C:/Program Files (x86)/IAR Systems/Embedded Workbench 7.5/arm/bin/iccarm.exe")
set(CMAKE_CXX_COMPILER  "C:/Program Files (x86)/IAR Systems/Embedded Workbench 7.5/arm/bin/iccarm.exe" "--eec++")

set(PROCESSOR "STM32F105RC")
set(DLIB_CONFIG "\"C:\\Program Files (x86)\\IAR Systems\\Embedded Workbench 7.5\\arm\\inc\\c\\DLib_Config_Normal.h\"")

set(CMAKE_C_FLAGS "${CPU_FLAGS} -e --dlib_config ${DLIB_CONFIG} -Ol --endian=little --fpu None --no_cse --no_unroll --no_inline --no_code_motion --no_tbaa --no_clustering --no_scheduling --debug" CACHE STRING "")
set(CMAKE_ASM_FLAGS "-s+ -M'<>' -w+ -r ${CPU_FLAGS} --fpu None" CACHE STRING "")
set(CMAKE_CXX_FLAGS "${CPU_FLAGS}" CACHE STRING "")

set(LINKER_SCRIPT "\"C:\\Program Files (x86)\\IAR Systems\\Embedded Workbench 7.5\\arm\\config\\linker\\ST\\stm32f105xC.icf\"")
set(CMAKE_EXE_LINKER_FLAGS "${CPU_FLAGS} --config ${LINKER_SCRIPT} --semihosting --entry __iar_program_start --vfe"  CACHE STRING "")

include_directories(
    "D:/PVE/STM/STM32F10x_StdPeriph_Lib_V3.5.0/Libraries/CMSIS/CM3/DeviceSupport/ST/STM32F10x"
    "D:/PVE/CMSIS/CMSIS/Include"
    "D:/PVE/STM/STM32F10x_StdPeriph_Lib_V3.5.0/Libraries/STM32F10x_StdPeriph_Driver/inc"
    "D:/PVE/Decawave/dw1000_api_rev2p02/decadriver"
    "D:/PVE/Decawave/dw1000_api_rev2p02/platform"
)

aux_source_directory("D:/PVE/STM/STM32F10x_StdPeriph_Lib_V3.5.0/Libraries/CMSIS/CM3/DeviceSupport/ST/STM32F10x" STM32_SRC)
aux_source_directory("D:/PVE/Decawave/dw1000_api_rev2p02/decadriver" DECAWAVE_DRV)
aux_source_directory("D:/PVE/Decawave/dw1000_api_rev2p02/platform" DECAWAVE_PLATFORM)
aux_source_directory("D:/PVE/STM/STM32F10x_StdPeriph_Lib_V3.5.0/Libraries/STM32F10x_StdPeriph_Driver/src" STM32_PL_SRC)
add_definitions(-DSTM32F10X_CL -DUSE_STDPERIPH_DRIVER)

# search for programs in the build host directories
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
# for libraries and headers in the target directories
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

project(stm32 C ASM)

set(SRC main.c)
set(ASM_STARTUP "D:/PVE/STM/STM32F10x_StdPeriph_Lib_V3.5.0/Libraries/CMSIS/CM3/DeviceSupport/ST/STM32F10x/startup/iar/startup_stm32f10x_cl.s")

add_library(stm32_periph_drv ${STM32_PL_SRC})
add_library(deca_platform ${DECAWAVE_PLATFORM})
add_library(deca_drv ${DECAWAVE_DRV})
add_library(stm32_platform ${ASM_STARTUP} ${STM32_SRC})

add_executable(main ${SRC})
target_link_libraries(main stm32_platform stm32_periph_drv deca_drv deca_platform)
