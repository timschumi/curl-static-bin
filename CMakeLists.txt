cmake_minimum_required(VERSION 3.12)

add_library(curl-phony INTERFACE)
target_include_directories(curl-phony INTERFACE include)

if("${CURL_HTTPONLY}")
	  set(CURL_PHONY_LINKLIST curl-httponly)
else()
	  set(CURL_PHONY_LINKLIST curl)
endif()

# zlib is always a dependency
list(APPEND CURL_PHONY_LINKLIST z)

# Add openssl if we are on Linux
if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
	list(APPEND CURL_PHONY_LINKLIST ssl crypto)
endif()

# Add -x64 suffix if we are building for x64
if(${CMAKE_SYSTEM_PROCESSOR} MATCHES "x64")
	list(TRANSFORM CURL_PHONY_LINKLIST APPEND "-x64")
endif()

# Add lib- library prefix for MSVC
if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	list(TRANSFORM CURL_PHONY_LINKLIST PREPEND "lib")
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	list(APPEND CURL_PHONY_LINKLIST crypt32 ws2_32 bcrypt secur32 iphlpapi)
	if(NOT "${CURL_HTTPONLY}")
		list(APPEND CURL_PHONY_LINKLIST wldap32)
	endif()
endif()

# Resolve the correct link directory
string(TOLOWER ${CMAKE_SYSTEM_NAME} CURL_PHONY_LINKDIR)
string(PREPEND CURL_PHONY_LINKDIR "lib/")

# Actually set all the things
target_link_directories(curl-phony INTERFACE ${CURL_PHONY_LINKDIR})
target_link_libraries(curl-phony INTERFACE ${CURL_PHONY_LINKLIST})

target_compile_definitions(curl-phony INTERFACE "CURL_STATICLIB=1")
