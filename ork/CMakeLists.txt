set(LIBNAME "ork")

# Sources
include_directories("${PROJECT_SOURCE_DIR}" "${PROJECT_SOURCE_DIR}/libraries" "${PROJECT_SOURCE_DIR}/libraries/*")
file(GLOB SOURCE_FILES *.cpp */*.cpp)

# Libraries
IF(WIN32)
	SET(LIBS OpenGL32 GLU32 GLEW32 pthreadVC2 stb_image tinyxml)
ELSE(WIN32)
	SET(LIBS GL GLU GLEW pthread stb_image tinyxml Xinerama Xcursor)
ENDIF(WIN32)

if(UNIX)
	set(LIBS ${LIBS} rt)
endif(UNIX)

#message(STATUS "Using libs: " ${LIBS})
#message(STATUS "GLFW needs: " ${GLFW_STATIC_LIBRARIES})
# Static or shared?
set(LIBTYPE STATIC)
set(GLFWLIBS ${GLFW_STATIC_LIBRARIES})
if(BUILD_SHARED)
	set(LIBTYPE SHARED)
    set(GLFWLIBS glfw3)
endif(BUILD_SHARED)
set(LIBS ${LIBS} ${GLFWLIBS})
message(STATUS "GLFW libs for " ${LIBTYPE} " library: " ${GLFWLIBS})

# Create a static library
IF(BUILD_SHARED)
	LINK_DIRECTORIES(${PROJECT_SOURCE_DIR}/lib ${Glew_LIBRARY_DIRS} ${GLFW_LIBRARY_DIRS})
ELSE(BUILD_SHARED)
	LINK_DIRECTORIES(${PROJECT_SOURCE_DIR}/lib)
ENDIF(BUILD_SHARED)
add_library(${LIBNAME} ${LIBTYPE} ${SOURCE_FILES})
target_link_libraries(${LIBNAME} ${LIBS})

# Adds SO Version and subversion. To be added to ensure ABI/API compatibility.
#SET_TARGET_PROPERTIES(${LIBNAME} PROPERTIES SOVERSION ${ORK_VERSION_MAJOR} VERSION ${ORK_VERSION})

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#link_directories(${PROJECT_SOURCE_DIR}/lib)

# Install headers
FOREACH(subdir core math render resource scenegraph taskgraph ui util)
  FILE(GLOB include-files ${subdir}/*.h)
  INSTALL(FILES ${include-files} DESTINATION include/ork/${subdir})
ENDFOREACH(subdir)

# Install library
IF(BUILD_SHARED)
	INSTALL(TARGETS ork
			LIBRARY DESTINATION ${LIB_INSTALL_DIR}
			ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
			RUNTIME DESTINATION ${LIB_INSTALL_DIR})
ELSE(BUILD_SHARED)
	INSTALL(TARGETS ork
			LIBRARY DESTINATION ${LIB_INSTALL_DIR}
			ARCHIVE DESTINATION ${LIB_INSTALL_DIR})
ENDIF(BUILD_SHARED)

message(STATUS "Setting ork cflags for installation package")
SET(ORK_CFLAGS "-DORK_API=__declspec(dllexport)" "-DTIXML_USE_STL" )
if(USE_SHARED_PTR)
	message(STATUS "Setting shared ptr usage to final package")
	set(ORK_CFLAGS ${ORK_CFLAGS} "-DUSE_SHARED_PTR")
endif(USE_SHARED_PTR)
if(USE_FREEGLUT)
	message(STATUS "Setting freeglut usage to final package")
	set(ORK_CFLAGS ${ORK_CFLAGS} "-DUSEFREEGLUT")
endif(USE_FREEGLUT)

CONFIGURE_FILE(ork.pc.in ${PROJECT_BINARY_DIR}/ork.pc @ONLY)
INSTALL(FILES ${PROJECT_BINARY_DIR}/ork.pc DESTINATION ${LIB_INSTALL_DIR}/pkgconfig)
