# Copyright (C) 2007-2011 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( alien C )
cmake_minimum_required ( VERSION 2.6 )
include ( dist.cmake )

if ( WIN32 )
 	add_definitions ( -DWINDOWS )
	if ( MSVC )
		add_definitions ( -D_MSC_VER )
	endif ()
elseif ( UNIX )
	add_definitions ( -DLINUX )
elseif ( APPLE )
	add_definitions( -DDARWIN )
endif ()

# Find LibFFI
find_path ( FFI_INCLUDE_DIR NAMES ffi.h 
	PATHS /usr/include/x86_64-linux-gnu/		# Ubuntu 9.10 - libffi-dev 3.0.1
	          /usr/include/i686-linux-gnu/                    # Ubuntu x86 9.10 - libffi-dev
)
include_directories ( ${FFI_INCLUDE_DIR} )
find_library ( FFI_LIBRARY NAMES ffi )

install_lua_module ( alien.core src/alien/core.c )
install_lua_module ( alien.struct src/alien/struct.c )
target_link_libraries ( alien_core ${FFI_LIBRARY} )
install_lua_module ( alien src/alien.lua )
install_doc ( doc/ )
install_example ( samples/ )
install_test ( tests/test_alien.lua )

# Testing....

# IMPROVE: integrate into dist.cmake?
add_custom_command ( OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/alien.lua
  COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/src/alien.lua ${CMAKE_CURRENT_BINARY_DIR}/alien.lua
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/alien.lua )
add_custom_target ( copy_alien ALL DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/alien.lua )

add_library ( alientest SHARED ../tests/alientest.c )

add_lua_test ( tests/test_alien.lua )

