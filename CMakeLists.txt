cmake_minimum_required(VERSION 3.31)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/.devcontainer/cmake)
include(preproject)
project(patch VERSION 2.7)
include(flags)
include(GNUInstallDirs)
set(XP_INSTALL_CMAKEDIR ${CMAKE_INSTALL_DATADIR}/cmake)
include(ExternalProject)
set_property(DIRECTORY PROPERTY "EP_BASE" ${CMAKE_BINARY_DIR}/epbase)
if(WIN32 AND NOT UNIX)
  set(VER 2.7.6)
  ExternalProject_Add(${PROJECT_NAME}
    DOWNLOAD_EXTRACT_TIMESTAMP true
    URL https://github.com/externpro/patch/releases/download/v2.7/patch-${VER}-bin.zip
    URL_HASH SHA256=eb848c8b0a51f51ed9407b832a975f1d47578825c7e7c3f23e79f23e88b9693b
    CONFIGURE_COMMAND ${CMAKE_COMMAND} -E copy_if_different
      <SOURCE_DIR>/patch.exe <INSTALL_DIR>/bin/patcz.exe
    BUILD_COMMAND ${CMAKE_COMMAND} -E copy_if_different
      <SOURCE_DIR>/msys-2.0.dll <INSTALL_DIR>/bin/
    INSTALL_COMMAND ""
    )
  set(patchExe bin/patcz)
  string(JOIN "\n" EXT3 # NOTE: --binary so it can handle line ending character issue
    "set(PATCH_CMD \${PATCH_EXE} --binary)"
    "list(APPEND reqVars PATCH_CMD)"
    ""
    )
else()
  set(VER 2.7.6)
  ExternalProject_Add(${PROJECT_NAME}
    DOWNLOAD_EXTRACT_TIMESTAMP true
    URL http://ftp.gnu.org/gnu/patch/patch-${VER}.tar.xz
    URL_HASH SHA256=ac610bda97abe0d9f6b7c963255a11dcb196c25e337c61f94e4778d632f1d8fd
    CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=<INSTALL_DIR>
    BUILD_COMMAND # use default
    INSTALL_COMMAND # use default
    )
  set(patchExe bin/patch)
  string(JOIN "\n" EXT3
    "set(PATCH_CMD \${PATCH_EXE})"
    "list(APPEND reqVars PATCH_CMD)"
    ""
    )
endif()
xpPackageDevel(EXE_PATH ${patchExe})
ExternalProject_Get_Property(${PROJECT_NAME} INSTALL_DIR)
install(DIRECTORY ${INSTALL_DIR}/ DESTINATION . USE_SOURCE_PERMISSIONS)
