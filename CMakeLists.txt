cmake_minimum_required(VERSION 3.9)

project(base_c_pro
        VERSION 0.1
        DESCRIPTION "Basic c/c++ project"
        LANGUAGES C CXX
        )

# ============================================================================
# DEPENDENCIES
# ============================================================================
set(CMAKE_SUPPORT_DIR "${CMAKE_SOURCE_DIR}/cmake")
option(DO_TEST "If need to do a test using gtest" ON)

function(cat IN_FILE OUT_FILE)
    file(READ ${IN_FILE} CONTENTS)
    file(APPEND ${OUT_FILE} "${CONTENTS}")
endfunction(cat IN_FILE OUT_FILE)

# gtest
set(DEPS_TMP_FILE "${CMAKE_BINARY_DIR}/deps_tmp_CMakeLists.txt.in")
configure_file("${CMAKE_SUPPORT_DIR}/deps_CMakeLists.txt.in" ${DEPS_TMP_FILE})
if(${DO_TEST})
    cat("${CMAKE_SUPPORT_DIR}/gtest_CMakeLists.txt.in" ${DEPS_TMP_FILE})
endif(${DO_TEST})

configure_file(${DEPS_TMP_FILE} "${CMAKE_SOURCE_DIR}/deps/deps-download/CMakeLists.txt")
execute_process(COMMAND "${CMAKE_COMMAND}" -G "${CMAKE_GENERATOR}" .
        WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}/deps/deps-download")
execute_process(COMMAND "${CMAKE_COMMAND}" --build .
        WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}/deps/deps-download")

# deps
add_subdirectory(deps)

# czasio
aux_source_directory(src SRC)
add_executable(run ${SRC})

# test
if(${DO_TEST})
    enable_testing()
    add_subdirectory(test)
    add_test(
        NAME test_example
        COMMAND test_example
    )
endif(${DO_TEST})