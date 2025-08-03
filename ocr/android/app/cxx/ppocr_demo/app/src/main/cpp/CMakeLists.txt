# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.10)

# set(CMAKE_SYSTEM_NAME linux)
# set(CMAKE_SYSTEM_NAME darwin) #mac build
#set(CMAKE_SYSTEM_NAME windows)
#if(ARM_TARGET_LANG STREQUAL "gcc")
#    set(ANDROID_ARM_NEON True)
#    set(CMAKE_ANDROID_NDK_TOOLCHAIN_VERSION ${ARM_TARGET_LANG})
#    set(ANDROID_TOOLCHAIN gcc)
#    set(CMAKE_TOOLCHAIN_FILE ${NDK_ROOT}/build/cmake/android.toolchain.cmake)
#    set(CMAKE_C_FLAGS gcc)
#    set(CMAKE_CXX_FLAGS g++)
#endif()
#
#if(ARM_TARGET_LANG STREQUAL "clang")
#    set(ANDROID_ARM_NEON True)
#    set(CMAKE_ANDROID_NDK_TOOLCHAIN_VERSION ${ARM_TARGET_LANG})
#    set(ANDROID_TOOLCHAIN clang)
#    set(ANDROID_PLATFORM "android-21")
#    if (${ARM_ABI} STREQUAL "arm64-v8a")
#        set(CMAKE_C_COMPILER ${NDK_ROOT}/toolchains/llvm/prebuilt/${CMAKE_SYSTEM_NAME}-x86_64/bin/aarch64-linux-android21-clang)
#        set(CMAKE_CXX_COMPILER ${NDK_ROOT}/toolchains/llvm/prebuilt/${CMAKE_SYSTEM_NAME}-x86_64/bin/aarch64-linux-android21-clang++)
#    else()
#        set(CMAKE_TOOLCHAIN_FILE ${NDK_ROOT}/build/cmake/android.toolchain.cmake)
#    endif()
#endif()

# CMAKE_CURRENT_SOURCE_DIR = Paddle-Lite-Demo/ocr/android/app/cxx/ppocr_demo/app/src/main/cpp
message(STATUS "CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")

# Creates and names a library, sets it as either STATIC or SHARED, and provides
# the relative paths to its source code. You can define multiple libraries, and
# CMake builds them for you. Gradle automatically packages shared libraries with
# your APK.

set(PaddleLite_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../PaddleLite")
message(STATUS "PaddleLite_DIR: ${PaddleLite_DIR}")
include_directories(${PaddleLite_DIR}/cxx/include)

#[[set(PaddleLite_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../../../../../libs/android/cxx")
message(STATUS "PaddleLite_DIR: ${PaddleLite_DIR}")
include_directories(${PaddleLite_DIR}/include)]]

set(OpenCV_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../OpenCV/sdk/native/jni")
find_package(OpenCV REQUIRED)
message(STATUS "OpenCV libraries: ${OpenCV_LIBS}")
include_directories(${OpenCV_INCLUDE_DIRS})

#[[set(OpenCV_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../../../../../../../../../libs/android/opencv4.1.0")
message(STATUS "OpenCV_DIR: ${OpenCV_DIR}")

set(OPENCV_PATH "${OpenCV_DIR}/${ARM_ABI}")
message(STATUS "OPENCV_PATH: ${OPENCV_PATH}")

set(OPENCV_INCLUDE_DIRS "${OPENCV_PATH}/include/")
set(OPENCV_LIBS  "${OPENCV_PATH}/libs/libopencv_java4.so")

include_directories(${OPENCV_INCLUDE_DIRS})
link_directories(${OPENCV_LIBS})]]
#
#set(CMAKE_CXX_FLAGS
#    "${CMAKE_CXX_FLAGS} -ffast-math -Ofast -Os -DNDEBUG -fno-exceptions -fomit-frame-pointer -fno-asynchronous-unwind-tables -fno-unwind-tables"
#)
#set(CMAKE_CXX_FLAGS
#    "${CMAKE_CXX_FLAGS} -fvisibility=hidden -fvisibility-inlines-hidden -fdata-sections -ffunction-sections -fexceptions"
#)
#set(CMAKE_SHARED_LINKER_FLAGS
#    "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--gc-sections -Wl,-z,nocopyreloc -Wl,-z,max-page-size=16384 -Wl,-z,common-page-size=16384")


set(CMAKE_CXX_FLAGS
        "${CMAKE_CXX_FLAGS} -ffast-math -Ofast -Os -DNDEBUG -fno-exceptions -fomit-frame-pointer -fno-asynchronous-unwind-tables -fno-unwind-tables"
)
set(CMAKE_CXX_FLAGS
        "${CMAKE_CXX_FLAGS} -fexceptions -fvisibility=hidden -fvisibility-inlines-hidden -fdata-sections -ffunction-sections"
)
set(CMAKE_SHARED_LINKER_FLAGS
        "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--gc-sections -Wl,-z,nocopyreloc -Wl,-z,max-page-size=16384 -Wl,-z,common-page-size=16384")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

add_library(
  # Sets the name of the library.
  Native
  # Sets the library as a shared library.
  SHARED
  # Provides a relative path to your source file(s).
  Native.cc pipeline.cc utils.cc cls_process.cc rec_process.cc det_process.cc db_post_process.cc clipper.cpp)

find_library(
  # Sets the name of the path variable.
  log-lib
  # Specifies the name of the NDK library that you want CMake to locate.
  log)

add_library(
  # Sets the name of the library.
  paddle_light_api_shared
  # Sets the library as a shared library.
  SHARED
  # Provides a relative path to your source file(s).
  IMPORTED)

set_target_properties(
  # Specifies the target library.
  paddle_light_api_shared
  # Specifies the parameter you want to define.
  PROPERTIES
    IMPORTED_LOCATION
    ${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libpaddle_light_api_shared.so
    # Provides the path to the library you want to import.
)


add_library(
        # Sets the name of the library.
        c++_shared
        # Sets the library as a shared library.
        SHARED
        # Provides a relative path to your source file(s).
        IMPORTED)

set_target_properties(
        # Specifies the target library.
        c++_shared
        # Specifies the parameter you want to define.
        PROPERTIES
        IMPORTED_LOCATION
        ${PaddleLite_DIR}/cxx/libs/${ANDROID_ABI}/libc++_shared.so
        # Provides the path to the library you want to import.
)

add_library(
        # Sets the name of the library.
        opencv
        # Sets the library as a shared library.
        SHARED
        # Provides a relative path to your source file(s).
        IMPORTED)

set_target_properties(
        # Specifies the target library.
        opencv
        # Specifies the parameter you want to define.
        PROPERTIES
        IMPORTED_LOCATION
        ${OpenCV_DIR}/../libs/${ANDROID_ABI}/libopencv_java4.so
        # Provides the path to the library you want to import.
)


# Specifies libraries CMake should link to your target library. You can link
# multiple libraries, such as libraries you define in this build script,
# prebuilt third-party libraries, or system libraries.

target_link_libraries(
  # Specifies the target library.
  Native
  paddle_light_api_shared
  c++_shared
  opencv
  GLESv2
  EGL
  jnigraphics
  ${log-lib}  -lz -llog)

string(REPLACE "-Werror=format-security" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
add_compile_options(-Wno-deprecated-declarations)
