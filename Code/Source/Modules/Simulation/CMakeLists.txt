set(lib ${SV_LIB_MODULE_SIMULATION_NAME})

#-----------------------------------------------------------------------------
# Create module
simvascular_create_module(
  TARGET ${lib}
  EXPORT_DIRECTIVE svSimulation
  SHARED_LIB ${SV_USE_MODULES_SHARED_LIBRARIES}
  LIBRARY_DEPENDS
  ${QT_LIBRARIES}
  ${VTK_LIBRARIES}
  ${ITK_LIBRARIES}
  ${MITK_LIBRARIES}
  ${SV_LIB_GEOM_NAME}
  ${SV_LIB_MODULE_MESH_NAME})
#-----------------------------------------------------------------------------

if(SV_INSTALL_LIBS)
  install(TARGETS ${lib}
    RUNTIME DESTINATION ${SV_INSTALL_RUNTIME_DIR} COMPONENT CoreExecutables
    LIBRARY DESTINATION ${SV_INSTALL_LIBRARY_DIR} COMPONENT CoreLibraries
    ARCHIVE DESTINATION ${SV_INSTALL_ARCHIVE_DIR} COMPONENT CoreLibraries
  )
endif()

if(SV_INSTALL_HEADERS)
  set(MODULE_FILES_CMAKE files.cmake)
  if(NOT IS_ABSOLUTE ${MODULE_FILES_CMAKE})
    set(MODULE_FILES_CMAKE ${CMAKE_CURRENT_SOURCE_DIR}/${MODULE_FILES_CMAKE})
  endif()
  if (EXISTS ${MODULE_FILES_CMAKE})
    include(${MODULE_FILES_CMAKE})
  endif()
  install(FILES ${H_FILES}
    DESTINATION ${SV_INSTALL_INCLUDE_DIR}/core COMPONENT CoreHeaders
  )
endif()

