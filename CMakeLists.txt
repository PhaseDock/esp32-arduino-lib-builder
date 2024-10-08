# The following lines of boilerplate have to be in your project's
# CMakeLists in this exact order for cmake to work correctly
cmake_minimum_required(VERSION 3.5)
#BOYD this didn't seem to be in 4.4, it might need to go
set(ENV{BLUEPAD32_ARDUINO} TRUE)

set(RMAKER_PATH ${CMAKE_SOURCE_DIR}/components/esp-rainmaker)
set(EXTRA_COMPONENT_DIRS ${RMAKER_PATH}/components/esp-insights/components ${RMAKER_PATH}/components)

include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(arduino-lib-builder)

idf_build_get_property(elf EXECUTABLE GENERATOR_EXPRESSION)

add_custom_command(
	OUTPUT "idf_libs"
	COMMAND ${CMAKE_SOURCE_DIR}/tools/copy-libs.sh ${IDF_TARGET} "${CONFIG_LIB_BUILDER_FLASHMODE}" "${CONFIG_SPIRAM_MODE_OCT}" "${CONFIG_IDF_TARGET_ARCH_XTENSA}"
	DEPENDS ${elf}
	WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
	VERBATIM
)
add_custom_target(idf-libs DEPENDS "idf_libs")

add_custom_command(
	OUTPUT "copy_bootloader"
	COMMAND ${CMAKE_SOURCE_DIR}/tools/copy-bootloader.sh ${IDF_TARGET} "${CONFIG_LIB_BUILDER_FLASHMODE}" "${CONFIG_LIB_BUILDER_FLASHFREQ}"
	DEPENDS bootloader
	WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
	VERBATIM
)
add_custom_target(copy-bootloader DEPENDS "copy_bootloader")

add_custom_command(
	OUTPUT "mem_variant"
	COMMAND ${CMAKE_SOURCE_DIR}/tools/copy-mem-variant.sh ${IDF_TARGET} "${CONFIG_LIB_BUILDER_FLASHMODE}" "${CONFIG_SPIRAM_MODE_OCT}"
	DEPENDS ${elf}
	WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
	VERBATIM
)
add_custom_target(mem-variant DEPENDS "mem_variant")
