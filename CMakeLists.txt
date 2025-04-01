cmake_minimum_required(VERSION 3.15)
project(Ant_World)

# Установка стандарта C++
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Получаем корректный абсолютный путь, обернутый в кавычки
file(TO_CMAKE_PATH "${CMAKE_SOURCE_DIR}" PROJECT_ROOT)
set(RAYLIB_DIR "${PROJECT_ROOT}/lib/raylib-5.5")

message(STATUS "Current source dir: ${PROJECT_ROOT}")
message(STATUS "Looking for raylib at: ${RAYLIB_DIR}")

# Проверяем существование критичных файлов raylib
set(RAYLIB_FOUND TRUE)

if(NOT EXISTS "${RAYLIB_DIR}/include/raylib.h")
    message(WARNING "raylib.h not found at ${RAYLIB_DIR}/include/raylib.h")
    set(RAYLIB_FOUND FALSE)
endif()

if(NOT EXISTS "${RAYLIB_DIR}/lib/libraylib.a" AND NOT EXISTS "${RAYLIB_DIR}/lib/libraylib.so")
    message(WARNING "Raylib library not found at ${RAYLIB_DIR}/lib/")
    set(RAYLIB_FOUND FALSE)
endif()

if(RAYLIB_FOUND)
    message(STATUS "Raylib found successfully")

    include_directories(${RAYLIB_DIR}/include)
    add_executable(Ant_World src/main.cpp)

    if(WIN32)
        target_link_libraries(Ant_World PRIVATE
                "${RAYLIB_DIR}/lib/libraylib.a"
                winmm gdi32 opengl32)
    elseif(APPLE)
        target_link_libraries(Ant_World PRIVATE
                "${RAYLIB_DIR}/lib/libraylib.a"
                "-framework IOKit -framework Cocoa -framework OpenGL")
    else()
        target_link_libraries(Ant_World PRIVATE
                "${RAYLIB_DIR}/lib/libraylib.so"
                pthread dl m)

        add_custom_command(TARGET Ant_World POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E copy
                "${RAYLIB_DIR}/lib/libraylib.so.550"
                $<TARGET_FILE_DIR:Ant_World>)
    endif()
else()
    message(FATAL_ERROR "\n\n"
            "Raylib not found at: ${RAYLIB_DIR}\n"
            "Please verify:\n"
            "1. The path exists and contains raylib-5.5 directory\n"
            "2. The directory contains include/raylib.h and lib/ files\n"
            "3. Try moving project to path without spaces or special characters\n"
            "Current path: ${PROJECT_ROOT}\n")
endif()

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)