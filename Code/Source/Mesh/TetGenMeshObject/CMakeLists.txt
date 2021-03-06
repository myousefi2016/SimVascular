##=========================================================================
 #
 # Copyright (c) 2014-2015 The Regents of the University of California.
 # All Rights Reserved.
 #
 # Permission is hereby granted, free of charge, to any person obtaining
 # a copy of this software and associated documentation files (the
 # "Software"), to deal in the Software without restriction, including
 # without limitation the rights to use, copy, modify, merge, publish,
 # distribute, sublicense, and/or sell copies of the Software, and to
 # permit persons to whom the Software is furnished to do so, subject
 # to the following conditions:
 #
 # The above copyright notice and this permission notice shall be included
 # in all copies or substantial portions of the Software.
 #
 # THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS
 # IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED
 # TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
 # PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER
 # OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
 # EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
 # PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
 # PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
 # LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
 # NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
 # SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 #
 #=========================================================================#

#-----------------------------------------------------------------------------
# Plugin - TetGen Mesh
#-----------------------------------------------------------------------------
set(lib ${SV_LIB_TETGEN_MESH_NAME})
set(export_directive tetgen_mesh)
set(export_file_name svTetGenMeshExports.h)

LIST(APPEND PLUGINLIBS ${lib})
include_directories(${TETGEN_INCLUDE_DIR})

SET(CXXSRCS cvTetGenMeshObject.cxx cvTetGenMeshSystem.cxx cv_tetgenmesh_utils.cxx)
SET(HDRS cvTetGenMeshObject.h cvTetGenMeshSystem.h cv_tetgenmesh_utils.h)

add_library(${lib} ${SV_LIBRARY_TYPE} ${CXXSRCS} cv_tetgen_mesh_init.cxx)

if(SV_USE_VMTK)
  SET(OPTLIBS ${SV_LIB_VMTK_UTILS_NAME})
endif()

if(SV_USE_MMG)
  SET(OPTLIBS ${OPTLIBS} ${SV_LIB_MMG_MESH_NAME} ${MMG_LIBRARY})
endif()

target_link_libraries(${lib}
   ${VTK_LIBRARIES} ${TCL_LIBRARY} ${TK_LIBRARY}
   ${OPTLIBS}
   ${TETGEN_LIBRARY}
   ${ZLIB_LIBRARY}
   ${SV_LIB_GLOBALS_NAME} ${SV_LIB_UTILS_NAME}
   ${SV_LIB_POLYDATA_SOLID_NAME} ${SV_LIB_MESH_NAME}
   )

set_target_properties(${lib} PROPERTIES "COMPILE_DEFINITIONS" TETLIBRARY)

# Set up for exports
string(TOUPPER ${export_directive} EXPORT_NAME)
set(_export_macro_names
  EXPORT_MACRO_NAME SV_EXPORT_${EXPORT_NAME}
  NO_EXPORT_MACRO_NAME SV_EXPORT_${EXPORT_NAME}_NO_EXPORT
  DEPRECATED_MACRO_NAME SV_EXPORT_${EXPORT_NAME}_DEPRECATED
  NO_DEPRECATED_MACRO_NAME SV_EXPORT_${EXPORT_NAME}_NO_DEPRECATED
  )

# Generate export headers
generate_export_header(${lib}
  ${_export_macro_names}
  EXPORT_FILE_NAME ${export_file_name})

if(SV_INSTALL_LIBS)
	install(TARGETS ${lib}
		RUNTIME DESTINATION ${SV_INSTALL_RUNTIME_DIR} COMPONENT PluginExecutables
		LIBRARY DESTINATION ${SV_INSTALL_LIBRARY_DIR} COMPONENT PluginLibraries
		ARCHIVE DESTINATION ${SV_INSTALL_ARCHIVE_DIR} COMPONENT PluginLibraries
		)
endif()
if(SV_INSTALL_HEADERS)
  install(FILES ${HDRS} ${CMAKE_CURRENT_BINARY_DIR}/${export_file_name}
		DESTINATION ${SV_INSTALL_INCLUDE_DIR}/core COMPONENT PluginHeaders
		)
endif()
