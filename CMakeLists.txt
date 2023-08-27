project(test C CXX)

find_package(OpenAL REQUIRED)

add_executable(test main.cpp)
target_link_libraries(test OpenAL::OpenAL)
