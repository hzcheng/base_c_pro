aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR} TEST_SRC)

add_executable(test_example ${TEST_SRC})
target_link_libraries(test_example gtest gtest_main)