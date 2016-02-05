cmake_minimum_required(VERSION 2.8)

project(Catch)

# define some folders
get_filename_component(CATCH_DIR "${CMAKE_CURRENT_SOURCE_DIR}" PATH)
get_filename_component(CATCH_DIR "${CATCH_DIR}" PATH)
set(SELF_TEST_DIR ${CATCH_DIR}/projects/SelfTest)
if(USE_CPP11)
  ## We can't turn this on by default, since it breaks on travis
  message(STATUS "Enabling C++11")
  set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}")
endif()

# define the sources of the self test
set(SOURCES
    ${SELF_TEST_DIR}/ApproxTests.cpp
    ${SELF_TEST_DIR}/BDDTests.cpp
    ${SELF_TEST_DIR}/ClassTests.cpp
    ${SELF_TEST_DIR}/ConditionTests.cpp
    ${SELF_TEST_DIR}/ExceptionTests.cpp
    ${SELF_TEST_DIR}/GeneratorTests.cpp
    ${SELF_TEST_DIR}/MessageTests.cpp
    ${SELF_TEST_DIR}/MiscTests.cpp
    ${SELF_TEST_DIR}/PartTrackerTests.cpp
    ${SELF_TEST_DIR}/TestMain.cpp
    ${SELF_TEST_DIR}/TrickyTests.cpp
    ${SELF_TEST_DIR}/VariadicMacrosTests.cpp
    ${SELF_TEST_DIR}/EnumToString.cpp
    ${SELF_TEST_DIR}/ToStringPair.cpp
    ${SELF_TEST_DIR}/ToStringVector.cpp
    ${SELF_TEST_DIR}/ToStringWhich.cpp
    ${SELF_TEST_DIR}/ToStringTuple.cpp
)

# configure the executable
include_directories(${CATCH_DIR}/include)
add_executable(SelfTest ${SOURCES})

# configure unit tests via CTest
enable_testing()
add_test(NAME RunTests COMMAND SelfTest)

add_test(NAME ListTests COMMAND SelfTest --list-tests)
set_tests_properties(ListTests PROPERTIES PASS_REGULAR_EXPRESSION "[0-9]+ test cases")

add_test(NAME ListTags COMMAND SelfTest --list-tags)
set_tests_properties(ListTags PROPERTIES PASS_REGULAR_EXPRESSION "[0-9]+ tags")

add_test(NAME ListTestsXml_ContainsCatchTestList COMMAND SelfTest -l -r xml)
set_tests_properties(ListTestsXml_ContainsCatchTestList PROPERTIES PASS_REGULAR_EXPRESSION "<CatchTestList name=\"[a-zA-Z0-9-_.\\:/ ]+\">.*</CatchTestList>")

add_test(NAME ListTestsXml_ContainsTestCase COMMAND SelfTest -l -r xml)
set_tests_properties(ListTestsXml_ContainsTestCase PROPERTIES PASS_REGULAR_EXPRESSION "<TestCase name=\".+\">.*<Source filename=\"[a-zA-Z0-9-_.\\:/ ]+\" line=\"[1-9][0-9]*\"/>.*</TestCase>")

add_test(NAME ListTestsXml_ContainsSource COMMAND SelfTest -l -r xml)
set_tests_properties(ListTestsXml_ContainsSource PROPERTIES PASS_REGULAR_EXPRESSION "<Source filename=\"[a-zA-Z0-9-_.\\:/ ]+\" line=\"[1-9][0-9]*\"/>")

add_test(NAME ListTestsXml_ContainsTags COMMAND SelfTest -l -r xml)
set_tests_properties(ListTestsXml_ContainsTags PROPERTIES PASS_REGULAR_EXPRESSION "<Tags>.*<Tag name=\"[a-zA-Z0-9-_.\\:/ ]+\"/>.*</Tags>")
