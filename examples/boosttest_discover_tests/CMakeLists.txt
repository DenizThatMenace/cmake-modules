#
# Example for `boosttest_discover_tests`
#

cmake_minimum_required(VERSION 3.0..3.18)
if(POLICY CMP0057)
  cmake_policy(SET CMP0057 NEW)
endif()

project(
  Example.BoostTestDiscoverTests
  VERSION 1.0.0
  LANGUAGES CXX
)

# We need CTest in order to successfully create test targets.
include(CTest)

# Use at least Boost 1.70, because it provides comfortable CMake export files.
# Note: You probably have to provide a hint (e.g. using the Boost_DIR variable)
#       where the Boost export file (`BoostConfig.cmake`) is located.
# Note: If you want to use CMake's FindBoost script (because you are looking
#       for an older Boost version) you have to modify this command properly!
find_package(Boost 1.70 CONFIG COMPONENTS unit_test_framework)

# Add directory with our modules to the modules search-path.
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../modules")


# Load the script with the `boosttest_discover_tests` function.
include(BoostTestDiscoverTests)


# The executable with our tests which uses Boost.Test as test-driver.
add_executable(BoostTest_example "${CMAKE_CURRENT_SOURCE_DIR}/example.cpp")
target_link_libraries(BoostTest_example PRIVATE Boost::unit_test_framework)


# By default, use the `PRE_TEST` discovery mode.
set(BOOSTTEST_DISCOVER_TESTS_DISCOVERY_MODE PRE_TEST)

# Discover the tests, in `POST_BUILD` mode, not skipping any tests, not even
# those which are disabled by default within the executable.
boosttest_discover_tests(BoostTest_example
  WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/test-dir"
  TEST_PREFIX "=1= "
  TEST_SUFFIX " =1="
  TEST_NAME_SEPARATOR " <--> "
  PROPERTIES
    WILL_FAIL FALSE  # Be explicit. (Although it is the default anyway.)
  DISCOVERY_TIMEOUT 1
  DISCOVERY_MODE POST_BUILD
)

# Discover the tests a second time, this time in `PRE_TEST` mode (as set to
# default by the `BOOSTTEST_DISCOVER_TESTS_DISCOVERY_MODE` variable), and skip
# those tests which are disabled by default within the executable.
boosttest_discover_tests(BoostTest_example
  WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/test-dir"
  TEST_PREFIX "=2= "
  TEST_SUFFIX " =2="
  TEST_NAME_SEPARATOR "::"
  SKIP_DISABLED_TESTS
  DISCOVERY_TIMEOUT 1
)
