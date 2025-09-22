cmake_minimum_required(VERSION 3.21)
project(CMakeSFMLProject LANGUAGES CXX)

include(FetchContent)
FetchContent_Declare(SFML
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG 2.6.x)
FetchContent_MakeAvailable(SFML)

##If we want different coloured output we need to install this cmake helper, we also need to make it available
FetchContent_Declare(
        CMakeColorHelper
        GIT_REPOSITORY https://github.com/LostInCompilation/CMake-Print-Colored-Text.git
        GIT_TAG main
)

# Now make it available
FetchContent_MakeAvailable(CMakeColorHelper)


add_executable(CMakeSFMLProject src/main.cpp src/ship.h src/ship.cpp src/game.h)
target_link_libraries(CMakeSFMLProject PRIVATE sfml-graphics)
target_compile_features(CMakeSFMLProject PRIVATE cxx_std_17)
if (WIN32 AND BUILD_SHARED_LIBS)
    add_custom_command(TARGET CMakeSFMLProject POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_RUNTIME_DLLS:CMakeSFMLProject> $<TARGET_FILE_DIR:CMakeSFMLProject> COMMAND_EXPAND_LISTS)
endif()
add_custom_target(copy_resources ALL COMMAND ${CMAKE_COMMAND}
        -E copy_directory
        "${PROJECT_SOURCE_DIR}/res"
         ${PROJECT_BINARY_DIR}/res
)
messageWithColor(COLOR BLUE "Dear students, if the following variables \n are not defined please let me know!\n
==============================
${PROJECT_SOURCE_DIR}/res and
${PROJECT_BINARY_DIR}/res\n" 
==============================
)


add_dependencies(CMakeSFMLProject copy_resources)
install(TARGETS CMakeSFMLProject)

## Space invaders
file(GLOB_RECURSE SOURCES src/*.cpp src/*.h)
add_executable(src ${SOURCES} src/ship.h src/ship.cpp src/game.h)
target_include_directories(src SYSTEM PRIVATE ${SFML_INCS})
target_link_libraries(src sfml-graphics)
