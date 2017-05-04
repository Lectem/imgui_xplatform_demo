cmake_minimum_required(VERSION 3.4)
project(imgui-sdl2-web)

if(CMAKE_SYSTEM_NAME MATCHES "Emscripten")
    set(IMGUI_IMPLEMENTATION_FILES source/imgui_impl_sdl_emscripten.cpp)
else()
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_LIST_DIR}/cmake")
    FIND_PACKAGE(OpenGL REQUIRED)
    FIND_PACKAGE(SDL2 REQUIRED)
    set(IMGUI_IMPLEMENTATION_FILES source/imgui_impl_sdl.cpp)
endif()


set(IMGUI_FILES imgui/imgui.cpp imgui/imgui_demo.cpp imgui/imgui_draw.cpp)
set(SOURCE_FILES source/main.cpp)

add_executable(imgui-sdl2-web ${SOURCE_FILES} ${IMGUI_IMPLEMENTATION_FILES} ${IMGUI_FILES})
target_include_directories(imgui-sdl2-web PUBLIC imgui ${SDL2_INCLUDE_DIR})
target_link_libraries(imgui-sdl2-web ${SDL2_LIBRARY} ${OPENGL_LIBRARIES} )

include(RunFixupBundle)
run_fixup(imgui-sdl2-web "" ${SDL2_LIBRARY_DIR})

set_property(TARGET imgui-sdl2-web PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(TARGET imgui-sdl2-web PROPERTY CXX_STANDARD 11)

if(CMAKE_SYSTEM_NAME MATCHES "Emscripten")
    #temp fix for emscripten, since it does not detect compiler features
    target_compile_options(imgui-sdl2-web PRIVATE "-std=c++11")
    set(CMAKE_EXECUTABLE_SUFFIX ".html")
    SET( CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} -s USE_SDL=2")
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    target_link_libraries(imgui-sdl2-web imm32 )
endif()
