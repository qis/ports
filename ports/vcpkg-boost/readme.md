# vcpkg-boost
1. Set `CMAKE_MSVC_RUNTIME_LIBRARY` and `BOOST_INSTALL_LAYOUT` during boost port configuration.

```diff
--- vcpkg-boost/boost-install.cmake
+++ vcpkg-boost/boost-install.cmake
@@ -40,6 +40,8 @@ project(Boost VERSION ${SEMVER_VERSION} LANGUAGES CXX)\n\
 set(BOOST_SUPERPROJECT_VERSION \${PROJECT_VERSION})\n\
 set(BOOST_SUPERPROJECT_SOURCE_DIR \"\${PROJECT_SOURCE_DIR}\")\n\
 list(APPEND CMAKE_MODULE_PATH \"${CURRENT_INSTALLED_DIR}/share/boost/cmake-build\")\n\
+set(CMAKE_MSVC_RUNTIME_LIBRARY \"MultiThreaded\$<\$<CONFIG:Debug>:DLL>\")\n\
+set(BOOST_INSTALL_LAYOUT \"system\")\n\
 include(BoostRoot)\n"
   )

```
