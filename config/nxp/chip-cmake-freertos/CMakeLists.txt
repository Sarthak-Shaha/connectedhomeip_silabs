#
# Copyright (c) 2024 Project CHIP Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

include(ExternalProject)

if(NOT CHIP_ROOT)
    get_filename_component(CHIP_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/../../.. REALPATH)
endif()

get_filename_component(GN_ROOT_TARGET ${CHIP_ROOT}/config/nxp/chip-gn-freertos REALPATH)

include(${CHIP_ROOT}/config/nxp/cmake/common.cmake)
include(${CHIP_ROOT}/src/app/chip_data_model.cmake)

# Prepare compiler flags
matter_add_cflags(${CMAKE_C_FLAGS})
matter_add_cxxflags(${CMAKE_CXX_FLAGS})

matter_get_compiler_flags_from_targets("${CONFIG_CHIP_EXTERNAL_TARGETS}")

# ==============================================================================
# Generate configuration for CHIP GN build system
# ==============================================================================
matter_add_gn_arg_string("nxp_nvm_component" ${CONFIG_CHIP_NVM_COMPONENT})
matter_add_gn_arg_string("nxp_platform" ${CONFIG_CHIP_NXP_PLATFORM})
matter_add_gn_arg("chip_with_factory_data" ${CONFIG_CHIP_FACTORY_DATA})
matter_add_gn_arg("chip_enable_secure_dac_private_key_storage" ${CONFIG_CHIP_SECURE_DAC_PRIVATE_KEY_STORAGE})
matter_add_gn_arg("chip_enable_secure_whole_factory_data" ${CONFIG_CHIP_ENABLE_SECURE_WHOLE_FACTORY_DATA})

matter_common_gn_args(
    DEBUG CONFIG_DEBUG
    LIB_SHELL CONFIG_CHIP_LIB_SHELL
    LIB_TESTS CONFIG_CHIP_BUILD_TESTS
    PROJECT_CONFIG ${CONFIG_CHIP_PROJECT_CONFIG}
    PROJECT_CONFIG_INC_DIR ${CONFIG_CHIP_PROJECT_CONFIG_INCLUDE_DIRS}
    DEVICE_INFO_EXAMPLE_PROVIDER CONFIG_CHIP_EXAMPLE_DEVICE_INFO_PROVIDER
)

matter_generate_args_tmp_file()

# ==============================================================================
# Build chip library
# ==============================================================================
matter_build(chip
    LIB_SHELL ${CONFIG_CHIP_LIB_SHELL}
    LIB_TESTS ${CONFIG_CHIP_BUILD_TESTS}
    DEVICE_INFO_EXAMPLE_PROVIDER ${CONFIG_CHIP_EXAMPLE_DEVICE_INFO_PROVIDER}
    GN_DEPENDENCIES ${CONFIG_GN_DEPENDENCIES}
)
