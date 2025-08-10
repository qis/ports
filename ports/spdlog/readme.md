# spdlog
1. Removed `fmt` dependency and enabled `std::format` support.

```diff
--- spdlog/portfile.cmake
+++ spdlog/portfile.cmake
@@ -26,7 +26,7 @@ vcpkg_cmake_configure(
     SOURCE_PATH "${SOURCE_PATH}"
     OPTIONS
         ${FEATURE_OPTIONS}
-        -DSPDLOG_FMT_EXTERNAL=ON
+        -DSPDLOG_USE_STD_FORMAT=ON
         -DSPDLOG_INSTALL=ON
         -DSPDLOG_BUILD_SHARED=${SPDLOG_BUILD_SHARED}
         -DSPDLOG_WCHAR_FILENAMES=${SPDLOG_WCHAR_FILENAMES}
@@ -43,10 +43,6 @@ if(NOT VCPKG_BUILD_TYPE)
 endif()

 # add support for integration other than cmake
-vcpkg_replace_string(${CURRENT_PACKAGES_DIR}/include/spdlog/tweakme.h
-    "// #define SPDLOG_FMT_EXTERNAL"
-    "#ifndef SPDLOG_FMT_EXTERNAL\n#define SPDLOG_FMT_EXTERNAL\n#endif"
-)
 if(SPDLOG_WCHAR_SUPPORT)
     vcpkg_replace_string(${CURRENT_PACKAGES_DIR}/include/spdlog/tweakme.h
         "// #define SPDLOG_WCHAR_TO_UTF8_SUPPORT"
@@ -61,7 +57,6 @@ if(SPDLOG_WCHAR_FILENAMES)
 endif()

 file(REMOVE_RECURSE
-    "${CURRENT_PACKAGES_DIR}/include/spdlog/fmt/bundled"
     "${CURRENT_PACKAGES_DIR}/debug/include"
     "${CURRENT_PACKAGES_DIR}/debug/share"
 )
--- spdlog/vcpkg.json
+++ spdlog/vcpkg.json
@@ -5,7 +5,6 @@
   "homepage": "https://github.com/gabime/spdlog",
   "license": "MIT",
   "dependencies": [
-    "fmt",
     {
       "name": "vcpkg-cmake",
       "host": true

```
