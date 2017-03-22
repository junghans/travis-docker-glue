file(GLOB MYM_SOURCES *.c)
add_library(mym ${MYM_SOURCES})
target_link_libraries(mym ${MATH_LIBRARIES})
install(TARGETS mym LIBRARY DESTINATION ${LIB} ARCHIVE DESTINATION ${LIB})

file(GLOB MYM_HEADERS *.h)
install(FILES ${MYM_HEADERS} DESTINATION include)

#optional
set_target_properties(mym PROPERTIES SOVERSION ${SOVERSION})
configure_file(libmym.pc.in ${CMAKE_CURRENT_BINARY_DIR}/libmym.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libmym.pc DESTINATION ${LIB}/pkgconfig) 
