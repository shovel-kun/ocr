cmake_minimum_required(VERSION 3.10)
project(guten-ocr)

set(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(OpenCV_DIR "${CMAKE_SOURCE_DIR}/build/opencv-mobile-4.9.0-android/sdk/native/jni")

file(GLOB SOURCES "./*.cpp" "../cpp/*.cpp")

find_package(OpenCV REQUIRED)
find_package(ReactAndroid REQUIRED CONFIG)

add_library(
  ${PROJECT_NAME}
  SHARED
  ${SOURCES}
)

include_directories(
  ../cpp
  ${OpenCV_INCLUDE_DIRS}
  ${CMAKE_SOURCE_DIR}/build/onnxruntime/headers
)

# https://github.com/react-native-community/discussions-and-proposals/discussions/816
# This if-then-else can be removed once this library does not support version below 0.76
if(ReactAndroid_VERSION_MINOR GREATER_EQUAL 76)
  target_link_libraries(
    ${PROJECT_NAME} 
    ${OpenCV_LIBS}
    ${CMAKE_SOURCE_DIR}/build/onnxruntime/jni/${CMAKE_ANDROID_ARCH_ABI}/libonnxruntime.so
    ReactAndroid::jsi
    ReactAndroid::reactnative
  )
else()
  target_link_libraries(
    ${PROJECT_NAME} 
    ${OpenCV_LIBS}
    ${CMAKE_SOURCE_DIR}/build/onnxruntime/jni/${CMAKE_ANDROID_ARCH_ABI}/libonnxruntime.so
    ReactAndroid::jsi
    ReactAndroid::reactnativejni
  )
endif()
