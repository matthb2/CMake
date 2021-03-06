# This directory contains tests that run CMake to configure a project
# but do not actually build anything.  To add a test:
#
# 1.) Add a subdirectory named for the test.
#
# 2.) Call add_RunCMake_test and pass the test directory name.
#
# 3.) Create a RunCMakeTest.cmake script in the directory containing
#   include(RunCMake)
#   run_cmake(SubTest1)
#   ...
#   run_cmake(SubTestN)
# where SubTest1..SubTestN are sub-test names each corresponding to
# an independent CMake run and project configuration.
#
# 3.) Create a CMakeLists.txt file in the directory containing
#   cmake_minimum_required(...)
#   project(${RunCMake_TEST} NONE) # or languages needed
#   include(${RunCMake_TEST}.cmake)
# where "${RunCMake_TEST}" is literal.  A value for RunCMake_TEST
# will be passed to CMake by the run_cmake macro when running each
# sub-test.
#
# 4.) Create a <SubTest>.cmake file for each sub-test named above
# containing the actual test code.  Optionally create files
# containing expected test results:
#   <SubTest>-result.txt  = Process result expected if not "0"
#   <SubTest>-stdout.txt  = Regex matching expected stdout content
#   <SubTest>-stderr.txt  = Regex matching expected stderr content
#   <SubTest>-check.cmake = Custom result check
# Note that trailing newlines will be stripped from actual test
# output before matching against the stdout and stderr expressions.
# The code in <SubTest>-check.cmake may use variables
#   RunCMake_TEST_SOURCE_DIR = Top of test source tree
#   RunCMake_TEST_BINARY_DIR = Top of test binary tree
# and an failure must store a message in RunCMake_TEST_FAILED.

macro(add_RunCMake_test test)
  add_test(RunCMake.${test} ${CMAKE_CMAKE_COMMAND}
    -DCMAKE_MODULE_PATH=${CMAKE_CURRENT_SOURCE_DIR}
    -DRunCMake_GENERATOR=${CMAKE_TEST_GENERATOR}
    -DRunCMake_SOURCE_DIR=${CMAKE_CURRENT_SOURCE_DIR}/${test}
    -DRunCMake_BINARY_DIR=${CMAKE_CURRENT_BINARY_DIR}/${test}
    -P "${CMAKE_CURRENT_SOURCE_DIR}/${test}/RunCMakeTest.cmake"
    )
endmacro()

add_RunCMake_test(GeneratorExpression)
add_RunCMake_test(Languages)
add_RunCMake_test(ObjectLibrary)

add_RunCMake_test(build_command)
add_RunCMake_test(find_package)
add_RunCMake_test(include)
add_RunCMake_test(list)

if("${CMAKE_TEST_GENERATOR}" MATCHES "Visual Studio [^6]")
  add_RunCMake_test(include_external_msproject)
endif()
