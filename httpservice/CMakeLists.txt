set(_srcs
  src/HttpConstants.cpp
  src/HttpServlet.cpp
  src/ServletContainer.cpp
  src/ServletContext.cpp
  src/HttpOutputStreamBuffer.cpp
  src/HttpServletRequest.cpp
  src/HttpServletResponse.cpp
  src/ServletConfig.cpp
)

set(_private_headers
  src/HttpOutputStreamBuffer.h
  src/HttpServletPrivate.h
  src/HttpServletRequestPrivate.h
  src/HttpServletResponsePrivate.h
  src/ServletConfigPrivate.h
  src/ServletContainerPrivate.h
)

set(_public_headers
  include/cppmicroservices/httpservice/HttpConstants.h
  include/cppmicroservices/httpservice/HttpServlet.h
  include/cppmicroservices/httpservice/ServletContext.h
  include/cppmicroservices/httpservice/HttpServletRequest.h
  include/cppmicroservices/httpservice/HttpServletResponse.h
  include/cppmicroservices/httpservice/ServletConfig.h
  include/cppmicroservices/httpservice/ServletContainer.h
)

set(compile_definitions USE_WEBSOCKET)
if(WIN32)
  list(APPEND compile_definitions WIN32)
elseif(LINUX)
  list(APPEND compile_definitions LINUX)
elseif(APPLE)
  list(APPEND compile_definitions DARWIN)
endif()

set(THREADS_PREFER_PTHREAD_FLAG 1)
find_package(Threads REQUIRED)
find_package(civetweb REQUIRED)

usMacroCreateBundle(HttpService
  VERSION "0.1.0"
  DEPENDS Framework
  PRIVATE_INCLUDE_DIRS ../third_party
  PUBLIC_HEADERS ${_public_headers}
  PRIVATE_HEADERS ${_private_headers}
  SOURCES ${_srcs}
  RESOURCES manifest.json
)

if(CMAKE_THREAD_LIBS_INIT)
  target_link_libraries(usHttpService PRIVATE ${CMAKE_THREAD_LIBS_INIT})
endif()

target_link_libraries(usHttpService PRIVATE civetweb::civetweb-cpp)

if(MINGW)
  target_link_libraries(usHttpService PRIVATE Ws2_32)
endif()

# Needed for clock_gettime with glibc < 2.17
if(UNIX AND NOT APPLE)
  target_link_libraries(usHttpService PRIVATE rt)
endif()

