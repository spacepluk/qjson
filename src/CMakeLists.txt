# add_custom_command (OUTPUT ${qjson_SOURCE_DIR}/lib/json_parser.cc
#                    PRE_BUILD
#                    COMMAND bison -t -o json_parser.cc -d json_parser.yy
#                    DEPENDS json_parser.yy
#                    WORKING_DIRECTORY ${qjson_SOURCE_DIR}/lib/
#                    )

# To regenerate json_scanner.cc use:
# flex json_scanner.yy

set(qjson_MOC_HDRS
  parserrunnable.h
  serializerrunnable.h
)

IF (NOT Qt5Core_FOUND)
  qt4_wrap_cpp(qjson_MOC_SRCS ${qjson_MOC_HDRS})
ENDIF()

set (qjson_SRCS parser.cpp qobjecthelper.cpp json_scanner.cpp json_parser.cc parserrunnable.cpp serializer.cpp serializerrunnable.cpp)
set (qjson_HEADERS parser.h parserrunnable.h qobjecthelper.h serializer.h serializerrunnable.h qjson_export.h)

# Required to use the intree copy of FlexLexer.h
INCLUDE_DIRECTORIES(.)

add_library (qjson SHARED ${qjson_SRCS} ${qjson_MOC_SRCS} ${qjson_HEADERS})
IF (Qt5Core_FOUND)
  target_link_libraries( qjson ${Qt5Core_LIBRARIES})
ELSE()
  target_link_libraries( qjson ${QT_LIBRARIES})
ENDIF()

if(NOT android)
	set_target_properties(qjson PROPERTIES
	                      VERSION ${QJSON_LIB_MAJOR_VERSION}.${QJSON_LIB_MINOR_VERSION}.${QJSON_LIB_PATCH_VERSION}
                              SOVERSION ${QJSON_LIB_MAJOR_VERSION}
	                      )
endif()
set_target_properties(qjson PROPERTIES
                      DEFINE_SYMBOL QJSON_MAKEDLL
                      PUBLIC_HEADER "${qjson_HEADERS}"
                      FRAMEWORK ${OSX_FRAMEWORK}
                      )

INSTALL(TARGETS qjson EXPORT qjson-export
   LIBRARY DESTINATION ${LIB_INSTALL_DIR}
   RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
   ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
   FRAMEWORK DESTINATION ${FRAMEWORK_INSTALL_DIR}
   PUBLIC_HEADER DESTINATION ${INCLUDE_INSTALL_DIR}/qjson
)
