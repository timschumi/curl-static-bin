cmake_minimum_required(VERSION 3.12)

add_library(curl-phony INTERFACE)
target_include_directories(curl-phony INTERFACE include)

# Add the base library to the Link list
set(CURL_PHONY_LINKLIST curl)

# Add openssl and zlib libraries if we are on Linux
if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
	list(APPEND CURL_PHONY_LINKLIST ssl crypto z)
endif()

# Add -x64 suffix if we are building for x64
if(${CMAKE_SYSTEM_PROCESSOR} MATCHES "x64")
	list(TRANSFORM CURL_PHONY_LINKLIST APPEND "-x64")
endif()

# Add lib- library prefix for Windows
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	list(TRANSFORM CURL_PHONY_LINKLIST PREPEND "lib")
endif()

# Link crypt32, WinSock2, wldap32 and Normaliz if we are on Windows
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	list(APPEND CURL_PHONY_LINKLIST crypt32 ws2_32 wldap32 normaliz)
endif()

# Resolve the correct link directory
string(TOLOWER ${CMAKE_SYSTEM_NAME} CURL_PHONY_LINKDIR)
string(PREPEND CURL_PHONY_LINKDIR "lib/")

# Actually set all the things
target_link_directories(curl-phony INTERFACE ${CURL_PHONY_LINKDIR})
target_link_libraries(curl-phony INTERFACE ${CURL_PHONY_LINKLIST})

target_compile_definitions(curl-phony INTERFACE "CURL_STATICLIB=1")
