cmake_minimum_required(VERSION 2.6)
project (hamcast_libscribe_module)

if (CMAKE_BUILD_TYPE STREQUAL Debug)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -DHC_ENABLE_LOGGING -g -Wextra -Wall -pedantic")
else ()
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O3")
endif (CMAKE_BUILD_TYPE STREQUAL Debug)
### uncomment if compiling for planetlab or other x86 systems
#set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=i486")

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
endif()

# Set up environment paths to cmake modules and libhamcast
set (CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR})

#set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

set (LIBSCRIBE_INCLUDE include/)
set (LIBSCRIBE_SRC  src/scribe_module.cpp 
                    src/scribe_instance.cpp
                    src/scribe_group.cpp)

# search for libs
find_package (Threads REQUIRED)
find_package (LibHamcast REQUIRED)
find_package (LibChimera REQUIRED)
find_package (OpenSSL REQUIRED)

include_directories (   ${LIBSCRIBE_INCLUDE} 
                        ${HAMCAST_INCLUDE}
                        ${CHIMERA_INCLUDE}
                        ${OPENSSL_INCLUDE_DIR})

add_library(libscribemodule SHARED ${LIBSCRIBE_SRC})

target_link_libraries (libscribemodule ${CMAKE_LD_LIBS}
                            ${CMAKE_THREAD_LIBS_INIT}
                            ${HAMCAST_LIBRARY}
                            ${CHIMERA_LIBRARY}
                            ${OPENSSL_LIBRARIES})

# install lib
set (LIBSCRIBE_VERSION_MAJOR 0)
set (LIBSCRIBE_VERSION_MINOR 15)
set (LIBSCRIBE_VERSION_PATCH 1)
set(LIBRARY_VERSION ${LIBSCRIBE_VERSION_MAJOR}.${LIBSCRIBE_VERSION_MINOR}.${LIBSCRIBE_VERSION_PATCH})
set(LIBRARY_SOVERSION ${LIBSCRIBE_VERSION_MAJOR})
set_target_properties(libscribemodule PROPERTIES 
                        SOVERSION ${LIBRARY_SOVERSION}
                        VERSION ${LIBRARY_VERSION}
                        OUTPUT_NAME scribemodule)
