cmake_minimum_required(VERSION 3.22.1)

set(CMAKE_C_COMPILER_WORKS TRUE)

# Edit project name
project(gnu-arm-template)

enable_language(C ASM)
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

# Add C source files
set(C_SOURCES
)

# Add assembly files
set(ASM_SOURCES
)

set(EXECUTABLE ${PROJECT_NAME}.elf)

add_executable(${EXECUTABLE} ${C_SOURCES} ${ASM_SOURCES})

# Add defines
target_compile_definitions(${EXECUTABLE} PRIVATE
)

# Add includes paths
target_include_directories(${EXECUTABLE} PRIVATE
)

# Add compile options
target_compile_options(${EXECUTABLE} PRIVATE
        # Add cpu options
        # -mcpu=cortex-m4
        # -mthumb
        # -mfpu=fpv4-sp-d16
        # -mfloat-abi=hard
        
        -Og

        -Wall
        -fdata-sections
        -ffunction-sections

        -g 
        -gdwarf-2
)

# Add link options
target_link_options(${EXECUTABLE} PRIVATE
        # Add cpu options
        # -mcpu=cortex-m4
        # -mthumb
        # -mfpu=fpv4-sp-d16
        # -mfloat-abi=hard
        
        -specs=nano.specs

        # Add linkfile file
        # -T/workspaces/Proejct/FLASH.ld
        -Wl,-Map=${PROJECT_NAME}.map,--cref
        -Wl,--gc-sections
        -Wl,--no-warn-rwx-segments
)

# Print file size
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND ${CMAKE_SIZE_UTIL} ${EXECUTABLE})

# Output to hex and binary files
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
        COMMAND ${CMAKE_OBJCOPY} -O binary ${EXECUTABLE} ${PROJECT_NAME}.bin)