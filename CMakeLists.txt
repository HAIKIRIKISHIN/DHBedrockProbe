cmake_minimum_required(VERSION 3.22)
project(DHBedrockProbe LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

include(FetchContent)
FetchContent_Declare(
    preloader_android
    GIT_REPOSITORY https://github.com/LiteLDev/preloader-android.git
    GIT_TAG 0.2.2
)
FetchContent_MakeAvailable(preloader_android)

add_library(dh_bedrock_probe SHARED
    src/main.cpp
    src/DHProbe.cpp
)

target_include_directories(dh_bedrock_probe PRIVATE
    "${preloader_android_SOURCE_DIR}/include"
)

target_compile_options(dh_bedrock_probe PRIVATE
    -fvisibility=hidden
    -fvisibility-inlines-hidden
)

target_link_options(dh_bedrock_probe PRIVATE
    "-Wl,-z,max-page-size=16384"
)

find_library(log-lib log REQUIRED)
target_link_libraries(dh_bedrock_probe PRIVATE
    preloader
    ${log-lib}
)

set_target_properties(dh_bedrock_probe PROPERTIES
    OUTPUT_NAME "dh_bedrock_probe"
)

set(MOD_ID "dh_bedrock_probe")
set(MOD_VERSION "0.1.0")
set(PACKAGE_ROOT "${CMAKE_BINARY_DIR}/package")
set(PACKAGE_MOD_DIR "${PACKAGE_ROOT}/${MOD_ID}")
set(PACKAGE_FILE "${CMAKE_BINARY_DIR}/${MOD_ID}-${MOD_VERSION}-${ANDROID_ABI}.levipack")

configure_file(manifest.json.in "${CMAKE_BINARY_DIR}/manifest.json" @ONLY)

add_custom_target(levi_package
    COMMAND "${CMAKE_COMMAND}" -E rm -rf "${PACKAGE_ROOT}"
    COMMAND "${CMAKE_COMMAND}" -E make_directory "${PACKAGE_MOD_DIR}"
    COMMAND "${CMAKE_COMMAND}" -E copy "$<TARGET_FILE:dh_bedrock_probe>" "${PACKAGE_MOD_DIR}/libdh_bedrock_probe.so"
    COMMAND "${CMAKE_COMMAND}" -E copy "${CMAKE_BINARY_DIR}/manifest.json" "${PACKAGE_MOD_DIR}/manifest.json"
    COMMAND "${CMAKE_COMMAND}" -E chdir "${PACKAGE_ROOT}" "${CMAKE_COMMAND}" -E tar "cf" "${PACKAGE_FILE}" --format=zip "${MOD_ID}"
    DEPENDS dh_bedrock_probe
    COMMENT "Packaging DH Bedrock Probe"
    VERBATIM
)
