cmake_minimum_required(VERSION 3.4.1)

set(PaddleLite_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../../PaddleLite")
include_directories(${PaddleLite_DIR}/cxx/include)

set(OpenCV_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../../OpenCV/sdk/native/jni")
message(STATUS "opencv dir: ${OpenCV_DIR}")
find_package(OpenCV REQUIRED)
message(STATUS "OpenCV libraries: ${OpenCV_LIBS}")
include_directories(${OpenCV_INCLUDE_DIRS})

aux_source_directory(. SOURCES)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ffast-math -Ofast -Os")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden -fvisibility-inlines-hidden -fdata-sections -ffunction-sections")
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--gc-sections -Wl,-z,nocopyreloc")
# Workaround: PaddleLite prebuilt .so has local symbols in global symbol table
# which NDK 25's lld linker rejects. Add --undefined-version to suppress the error.
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--undefined-version")

add_library(Native SHARED ${SOURCES})

find_library(log-lib log)

add_library(paddle_light_api_shared SHARED IMPORTED)
set_target_properties(paddle_light_api_shared PROPERTIES
    IMPORTED_LOCATION ${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libpaddle_light_api_shared.so)

target_link_libraries(Native
    paddle_light_api_shared
    ${OpenCV_LIBS}
    GLESv2
    EGL
    jnigraphics
    ${log-lib})

add_custom_command(TARGET Native POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    ${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libc++_shared.so
    ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/libc++_shared.so)

add_custom_command(TARGET Native POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    ${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libpaddle_light_api_shared.so
    ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/libpaddle_light_api_shared.so)

# Optional: copy NPU (HiAI) libs if they exist
file(GLOB HIAI_LIBS "${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libhiai*.so")
foreach(HIAI_LIB ${HIAI_LIBS})
    add_custom_command(TARGET Native POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${HIAI_LIB}
        ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/)
endforeach()
