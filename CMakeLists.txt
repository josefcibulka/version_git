cmake_minimum_required(VERSION 2.8)

project("version_from_git")

# Appends the cmake/modules path to MAKE_MODULE_PATH variable.
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules ${CMAKE_MODULE_PATH})

#
# Make a version file containing the current version from git.
#
if(EXISTS "${PROJECT_SOURCE_DIR}/versionForSourcePackage.cmake")
  include("${PROJECT_SOURCE_DIR}/versionForSourcePackage.cmake")
else()
  include(GetGitRevisionDescription)
  git_describe(VERSION --tags --dirty=-dirty)
endif()

#parse the version information into pieces.
string(REGEX REPLACE "^v([0-9]+)\\..*" "\\1" VERSION_MAJOR "${VERSION}")
string(REGEX REPLACE "^v[0-9]+\\.([0-9]+).*" "\\1" VERSION_MINOR "${VERSION}")
string(REGEX REPLACE "^v[0-9]+\\.[0-9]+\\.([0-9]+).*" "\\1" VERSION_PATCH "${VERSION}")
string(REGEX REPLACE "^v[0-9]+\\.[0-9]+\\.[0-9]+(.*)" "\\1" VERSION_SHA1 "${VERSION}")
set(VERSION_SHORT "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")

set(CPACK_SOURCE_GENERATOR "TGZ")
set(CPACK_PACKAGE_VERSION_MAJOR "${VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${VERSION_PATCH}")
# The following will transfer the version from git to the source package.
set(CPACK_SOURCE_PACKAGE_FILE_NAME "${PROJECT_NAME}-${VERSION_SHORT}-Source")
#set(CPACK_INSTALL_COMMANDS "${CMAKE_SOURCE_DIR}/versionNumberToPackage.sh ${PROJECT_BINARY_DIR}/versionForSourcePackage.cmake \
#    ${PROJECT_BINARY_DIR}/_CPack_Packages/")
set(CPACK_INSTALL_COMMANDS "${CMAKE_COMMAND} -E make_directory \
    ${PROJECT_BINARY_DIR}/_CPack_Packages/Linux-Source/${CPACK_SOURCE_GENERATOR}/${CPACK_SOURCE_PACKAGE_FILE_NAME}/"
    "${CMAKE_COMMAND} -E copy \
    ${PROJECT_BINARY_DIR}/versionForSourcePackage.cmake \
    ${PROJECT_BINARY_DIR}/_CPack_Packages/Linux-Source/${CPACK_SOURCE_GENERATOR}/${CPACK_SOURCE_PACKAGE_FILE_NAME}/")
# Exclude the build and .git directory from the source package.
set(CPACK_SOURCE_IGNORE_FILES "${PROJECT_SOURCE_DIR}/.git/;${PROJECT_BINARY_DIR}/;${CPACK_SOURCE_IGNORE_FILES}")

include (CPack)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/version.cpp.in
                ${CMAKE_CURRENT_BINARY_DIR}/version.cpp)
configure_file(
   ${PROJECT_SOURCE_DIR}/versionForSourcePackage.cmake.in
   ${PROJECT_BINARY_DIR}/versionForSourcePackage.cmake @ONLY)
set(version_file "${CMAKE_CURRENT_BINARY_DIR}/version.cpp")

set(source_files "${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp")

#Add the version_file to the project build.
add_executable(${PROJECT_NAME} ${source_files} ${version_file})


