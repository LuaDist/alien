# Copyright (C) 2007-2009 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

PROJECT (alien C)
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
INCLUDE(dist.cmake)

# We need Lua to compile
FIND_PACKAGE (Lua51 REQUIRED)
INCLUDE_DIRECTORIES( ${LUA_INCLUDE_DIR})

IF(WIN32)
	ADD_DEFINITIONS(-DWINDOWS)
	IF(MSVC)
		ADD_DEFINITIONS(-D_MSC_VER)
	ENDIF(MSVC)
ELSEIF(UNIX)
	ADD_DEFINITIONS(-DLINUX)
ELSEIF(APPLE)
	ADD_DEFINITIONS(-DDARWIN)
ENDIF()

# Find LibFFI
FIND_PATH(FFI_INCLUDE_DIR NAMES ffi.h 
	PATHS /usr/include/x86_64-linux-gnu/		# Ubuntu 9.10 - libffi-dev 3.0.1
	          /usr/include/i686-linux-gnu/                    # Ubuntu x86 9.10 - libffi-dev
)
INCLUDE_DIRECTORIES (${FFI_INCLUDE_DIR})
FIND_LIBRARY(FFI_LIBRARY NAMES ffi)

# Build modules
ADD_LUA_MODULE(core src/alien/core.c ${LIBFFI_SRC})
ADD_LUA_MODULE(struct src/alien/struct.c )
TARGET_LINK_LIBRARIES(core ${FFI_LIBRARY})

# Install all files and documentation
INSTALL(FILES src/alien.lua DESTINATION ${INSTALL_LMOD})
INSTALL(TARGETS core struct  DESTINATION ${INSTALL_CMOD}/alien)

INSTALL(DIRECTORY doc/ DESTINATION ${INSTALL_DOC})
INSTALL(DIRECTORY samples/ DESTINATION ${INSTALL_EXAMPLE})

INSTALL(FILES tests/test_alien.lua DESTINATION ${INSTALL_TEST})
