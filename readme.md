Repository that uses git describe and cmake to obtain version numbers and transfer them to the source package created by CPack.
To test it, run `make package_source` as (or after) the last step in the instructions below.

The original description from iPenguin/version_git:

This is a simple repository that uses git describe and cmake to automatically assign version numbers to a project when it's compiled.

It is provided under the MIT license.

Do the following to test it:

    cd version_git

    mkdir build && cd build

    cmake ..

    make


