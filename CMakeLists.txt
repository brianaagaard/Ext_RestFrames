#
# Package building RestFrames
#

# Set the package name:
atlas_subdir( RestFrames )

# In release recompilation mode stop here:
if( ATLAS_RELEASE_MODE )
   return()
endif()

# Decide whether to request debug symbols from the build:
if( "${CMAKE_BUILD_TYPE}" STREQUAL "Release" OR
      "${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel" OR
      "${CMAKE_BUILD_TYPE}" STREQUAL "" )
   set( _restFramesExtraConfig "--disable-debug" )
else()
   set( _restFramesExtraConfig "--enable-debug" )
endif()

# Temporary directory for the build results:
set( _buildDir
   ${CMAKE_CURRENT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/RestFramesBuild )

# Set up the build of RestFrames for the build area:
ExternalProject_Add( RestFrames
   PREFIX ${CMAKE_BINARY_DIR}
   URL https://github.com/crogan/RestFrames/archive/v1.0.1.tar.gz
   URL_MD5 668e3ca6f301172d7e67b5e85b1ab6d2
   INSTALL_DIR ${CMAKE_BINARY_DIR}/${ATLAS_PLATFORM}
   CONFIGURE_COMMAND ./configure
   --prefix=${_buildDir} --enable-shared --disable-static --enable-allcxxplugins
   ${_restFramesExtraConfig}
   BUILD_IN_SOURCE 1
   BUILD_COMMAND make
   COMMAND make install
   INSTALL_COMMAND ${CMAKE_COMMAND} -E copy_directory
   ${_buildDir}/ <INSTALL_DIR>
   )
add_dependencies( Package_RestFrames RestFrames )

# Set up its installation:
install( DIRECTORY ${_buildDir}/
   DESTINATION . USE_SOURCE_PERMISSIONS OPTIONAL )
