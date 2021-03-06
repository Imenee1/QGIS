add_executable(crssync main.cpp)

include_directories(
  ../core
  ../core/geometry
  ${CMAKE_SOURCE_DIR}/external
  ${CMAKE_BINARY_DIR}/src/core
)
include_directories(SYSTEM
  ${GDAL_INCLUDE_DIR}
  ${PROJ_INCLUDE_DIR}
)

target_link_libraries(crssync
  qgis_core
  ${PROJ_LIBRARY}
  ${GDAL_LIBRARY}
)

if(MSVC AND NOT USING_NMAKE)
  add_custom_target(synccrsdb
    COMMENT "Running crssync"
    COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${CMAKE_CFG_INTDIR}/crssync.exe
    DEPENDS crssync
  )
elseif()
  set(NATIVE_CRSSYNC_BIN CACHE PATH "Path to a natively compiled synccrsdb binary")
  if(NOT NATIVE_CRSSYNC_BIN)
    message(FATAL_ERROR "NATIVE_CRSSYNC_BIN needs to be defined when cross-compiling")
  endif()
  add_custom_target(synccrsdb
    COMMENT "Running native crssync"
    COMMAND ${NATIVE_CRSSYNC_BIN}
    DEPENDS crssync
  )
else()
  add_custom_target(synccrsdb
    COMMENT "Running crssync"
    COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/crssync
    DEPENDS crssync
  )
endif()

install(CODE "message(\"Installing crssync ...\")")
install(TARGETS crssync RUNTIME DESTINATION ${QGIS_LIBEXEC_DIR})
