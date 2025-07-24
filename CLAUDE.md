This is the source code to Python's CFFI library. We are on a branch where I want to introduce building iOS wheels. Those wheels are built with cibuildwheel, the configuration is in .github/workflows/ci.yaml. There are three types of iOS wheels: Intel simulator, ARM64 simulator, and ARM64 device. I'm currently not interested in Intel simulator wheels, so only ARM64 simulator and ARM64 device. iOS wheels are **always** cross-compiled so several assumptions in the build configuration of CFFI do not hold for those builds. Additionally, CFFI depends on libffi, which we want to statically compile into CFFI and **not** dynamically link from the operating system.

To add support for building iOS wheel, you have to introduce the following changes:

- add new entries in the macos matrix with the arm64_iphoneos and arm64_iphonesimulator specs;
- make setup.py for those specs not call use_pkg_config(), because iOS is cross-compiled;
- make setup.py for those specs not include system dirs for FFI, because iOS is cross-compiled;
- make the build for those specs download prebuilt `.a` file for libffi from https://github.com/beeware/cpython-apple-source-deps/releases (at the time of writing the most current version is libFFI 3.4.7 build 2; allow this to be configured by the user)
- make the build statically link the prebuilt `.a` libffi file

In case you do not know how to proceed, ask me for clarifications.
