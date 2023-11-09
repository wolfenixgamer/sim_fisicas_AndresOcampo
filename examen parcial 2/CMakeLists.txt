cmake_minimum_required(VERSION 3.26)
project(examen_parcial_2)

set(CMAKE_CXX_STANDARD 17)

add_executable(examen_parcial_2 main.cpp)

find_package(box2d REQUIRED)
target_link_libraries(examen_parcial_2 box2d::box2d)
find_package(SFML 2.5 COMPONENTS graphics REQUIRED)
target_link_libraries(Contact_Listener sfml-graphics)