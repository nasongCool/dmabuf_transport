cmake_minimum_required(VERSION 3.8)
project(dmabuf_transport)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -std=c++17)
endif()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# find dependencies
find_package(ament_cmake_auto REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(PCL REQUIRED)

ament_auto_find_build_dependencies()

ament_auto_add_library(${PROJECT_NAME} SHARED
  src/type/image.cpp
  src/type/point_cloud2.cpp
)

macro(add_test_component component_name class_name)
  ament_auto_add_library(${PROJECT_NAME}_${component_name}_component SHARED
    test/${component_name}.cpp
  )

  ament_target_dependencies(${PROJECT_NAME}_${component_name}_component
    rclcpp_components
    pcl_conversions
  )

  target_include_directories(${PROJECT_NAME}_${component_name}_component PUBLIC
    ${PCL_INCLUDE_DIRS}
  )

  target_link_libraries(${PROJECT_NAME}_${component_name}_component
    ${PCL_LIBRARIES}
  )

  rclcpp_components_register_nodes(${PROJECT_NAME}_${component_name}_component ${class_name})
endmacro()

add_test_component(test_pub "dmabuf_transport::TestPubComponent")
add_test_component(test_sub "dmabuf_transport::TestSubComponent")
add_test_component(test_dump "dmabuf_transport::TestDumpComponent")

ament_auto_package()
