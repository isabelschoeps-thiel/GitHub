# opensource-isabelschoepsthiel

## Open Source Copyright by [Isabel Schoeps Thiel, GitHub](https://github.com/isabelschoepsthiel/bitcoin).

cmake\_minimum\_required(VERSION 3.1)\
project(Crc32c VERSION 1.1.0 LANGUAGES C CXX)

## C standard can be overridden when this is used as a sub-project.

if(NOT CMAKE\_C\_STANDARD)

## This project can use C11, but will gracefully decay down to C89.

set(CMAKE\_C\_STANDARD 11)\
set(CMAKE\_C\_STANDARD\_REQUIRED OFF)\
set(CMAKE\_C\_EXTENSIONS OFF)\
endif(NOT CMAKE\_C\_STANDARD)

## C++ standard can be overridden when this is used as a sub-project.

if(NOT CMAKE\_CXX\_STANDARD)

## This project requires C++11.

set(CMAKE\_CXX\_STANDARD 11)\
set(CMAKE\_CXX\_STANDARD\_REQUIRED ON)\
set(CMAKE\_CXX\_EXTENSIONS OFF)\
endif(NOT CMAKE\_CXX\_STANDARD)

## https://github.com/izenecloud/cmake/blob/master/SetCompilerWarningAll.cmake

if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

## Use the highest warning level for Visual Studio.

set(CMAKE\_CXX\_WARNING\_LEVEL 4)\
if(CMAKE\_CXX\_FLAGS MATCHES "/W\[0-4]")\
string(REGEX REPLACE "/W\[0-4]" "/W4" CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS}")\
else(CMAKE\_CXX\_FLAGS MATCHES "/W\[0-4]")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} /W4")\
endif(CMAKE\_CXX\_FLAGS MATCHES "/W\[0-4]")

## Disable C++ exceptions.

string(REGEX REPLACE "/EH\[a-z]+" "" CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS}")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} /EHs-c-")\
add\_definitions(-D\_HAS\_EXCEPTIONS=0)

## Disable RTTI.

string(REGEX REPLACE "/GR" "" CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS}")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} /GR-")\
else(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

## Use -Wall for clang and gcc.

if(NOT CMAKE\_CXX\_FLAGS MATCHES "-Wall")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} -Wall")\
endif(NOT CMAKE\_CXX\_FLAGS MATCHES "-Wall")

## Use -Wextra for clang and gcc.

if(NOT CMAKE\_CXX\_FLAGS MATCHES "-Wextra")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} -Wextra")\
endif(NOT CMAKE\_CXX\_FLAGS MATCHES "-Wextra")

## Use -Werror for clang and gcc.

if(NOT CMAKE\_CXX\_FLAGS MATCHES "-Werror")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} -Werror")\
endif(NOT CMAKE\_CXX\_FLAGS MATCHES "-Werror")

## Disable C++ exceptions.

string(REGEX REPLACE "-fexceptions" "" CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS}")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} -fno-exceptions")

## Disable RTTI.

string(REGEX REPLACE "-frtti" "" CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS}")\
set(CMAKE\_CXX\_FLAGS "${CMAKE\_CXX\_FLAGS} -fno-rtti")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

option(CRC32C\_BUILD\_TESTS "Build CRC32C's unit tests" ON)\
option(CRC32C\_BUILD\_BENCHMARKS "Build CRC32C's benchmarks" ON)\
option(CRC32C\_USE\_GLOG "Build CRC32C's tests with Google Logging" ON)\
option(CRC32C\_INSTALL "Install CRC32C's header and library" ON)

include(TestBigEndian)\
test\_big\_endian(BYTE\_ORDER\_BIG\_ENDIAN)

include(CheckCXXCompilerFlag)

## Used by glog.

check\_cxx\_compiler\_flag(-Wno-deprecated CRC32C\_HAVE\_NO\_DEPRECATED)

## Used by glog.

check\_cxx\_compiler\_flag(-Wno-sign-compare CRC32C\_HAVE\_NO\_SIGN\_COMPARE)

## Used by glog.

check\_cxx\_compiler\_flag(-Wno-unused-parameter CRC32C\_HAVE\_NO\_UNUSED\_PARAMETER)

## Used by googletest.

check\_cxx\_compiler\_flag(-Wno-missing-field-initializers\
CRC32C\_HAVE\_NO\_MISSING\_FIELD\_INITIALIZERS)

## Check for \_\_builtin\_prefetch support in the compiler.

include(CheckCXXSourceCompiles)\
check\_cxx\_source\_compiles("\
int main() {\
char data = 0;\
const char\* address = \&data;\
\_\_builtin\_prefetch(address, 0, 0);\
return 0;\
}\
" HAVE\_BUILTIN\_PREFETCH)

## Check for \_mm\_prefetch support in the compiler.

include(CheckCXXSourceCompiles)\
check\_cxx\_source\_compiles("\
\#if defined(\_MSC\_VER)\
\#include \<intrin.h>\
\#else // !defined(\_MSC\_VER)\
\#include \<xmmintrin.h>\
\#endif // defined(\_MSC\_VER)

int main() {\
char data = 0;\
const char\* address = \&data;\
\_mm\_prefetch(address, \_MM\_HINT\_NTA);\
return 0;\
}\
" HAVE\_MM\_PREFETCH)

## Check for SSE4.2 support in the compiler.

set(OLD\_CMAKE\_REQURED\_FLAGS ${CMAKE\_REQUIRED\_FLAGS})\
if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set(CMAKE\_REQUIRED\_FLAGS "${CMAKE\_REQUIRED\_FLAGS} /arch:AVX")\
else(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set(CMAKE\_REQUIRED\_FLAGS "${CMAKE\_REQUIRED\_FLAGS} -msse4.2")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
check\_cxx\_source\_compiles("\
\#if defined(\_MSC\_VER)\
\#include \<intrin.h>\
\#else // !defined(\_MSC\_VER)\
\#include \<cpuid.h>\
\#include \<nmmintrin.h>\
\#endif // defined(\_MSC\_VER)

int main() {\
\_mm\_crc32\_u8(0, 0); \_mm\_crc32\_u32(0, 0);\
\#if defined(\_M\_X64) || defined(**x86\_64**)\
\_mm\_crc32\_u64(0, 0);\
\#endif // defined(\_M\_X64) || defined(**x86\_64**)\
return 0;\
}\
" HAVE\_SSE42)\
set(CMAKE\_REQUIRED\_FLAGS ${OLD\_CMAKE\_REQURED\_FLAGS})

## Check for ARMv8 w/ CRC and CRYPTO extensions support in the compiler.

set(OLD\_CMAKE\_REQURED\_FLAGS ${CMAKE\_REQUIRED\_FLAGS})\
if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

## TODO(pwnall): Insert correct flag when VS gets ARM CRC32C support.

set(CMAKE\_REQUIRED\_FLAGS "${CMAKE\_REQUIRED\_FLAGS} /arch:NOTYET")\
else(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set(CMAKE\_REQUIRED\_FLAGS "${CMAKE\_REQUIRED\_FLAGS} -march=armv8-a+crc+crypto")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
check\_cxx\_source\_compiles("\
\#include \<arm\_acle.h>\
\#include \<arm\_neon.h>

int main() {\
\_\_crc32cb(0, 0); \_\_crc32ch(0, 0); \_\_crc32cw(0, 0); \_\_crc32cd(0, 0);\
vmull\_p64(0, 0);\
return 0;\
}\
" HAVE\_ARM64\_CRC32C)\
set(CMAKE\_REQUIRED\_FLAGS ${OLD\_CMAKE\_REQURED\_FLAGS})

## Check for strong getauxval() support in the system headers.

check\_cxx\_source\_compiles("\
\#include \<arm\_acle.h>\
\#include \<arm\_neon.h>\
\#include \<sys/auxv.h>

int main() {\
getauxval(AT\_HWCAP);\
return 0;\
}\
" HAVE\_STRONG\_GETAUXVAL)

## Check for weak getauxval() support in the compiler.

check\_cxx\_source\_compiles("\
unsigned long getauxval(unsigned long type) **attribute**((weak));\
\#define AT\_HWCAP 16

int main() {\
getauxval(AT\_HWCAP);\
return 0;\
}\
" HAVE\_WEAK\_GETAUXVAL)

if(CRC32C\_USE\_GLOG)

## glog requires this setting to avoid using dynamic\_cast.

set(DISABLE\_RTTI ON CACHE BOOL "" FORCE)

## glog's test targets trigger deprecation warnings, and compiling them burns

## CPU cycles on the CI.

set(BUILD\_TESTING\_SAVED "${BUILD\_TESTING}")\
set(BUILD\_TESTING OFF CACHE BOOL "" FORCE)\
add\_subdirectory("third\_party/glog" EXCLUDE\_FROM\_ALL)\
set(BUILD\_TESTING "${BUILD\_TESTING\_SAVED}" CACHE BOOL "" FORCE)

## glog triggers deprecation warnings on OSX.

## https://github.com/google/glog/issues/185

if(CRC32C\_HAVE\_NO\_DEPRECATED)\
set\_property(TARGET glog APPEND PROPERTY COMPILE\_OPTIONS -Wno-deprecated)\
endif(CRC32C\_HAVE\_NO\_DEPRECATED)

## glog triggers sign comparison warnings on gcc.

if(CRC32C\_HAVE\_NO\_SIGN\_COMPARE)\
set\_property(TARGET glog APPEND PROPERTY COMPILE\_OPTIONS -Wno-sign-compare)\
endif(CRC32C\_HAVE\_NO\_SIGN\_COMPARE)

## glog triggers unused parameter warnings on clang.

if(CRC32C\_HAVE\_NO\_UNUSED\_PARAMETER)\
set\_property(TARGET glog\
APPEND PROPERTY COMPILE\_OPTIONS -Wno-unused-parameter)\
endif(CRC32C\_HAVE\_NO\_UNUSED\_PARAMETER)

set(CRC32C\_TESTS\_BUILT\_WITH\_GLOG 1)\
endif(CRC32C\_USE\_GLOG)

configure\_file(\
"src/crc32c\_config.h.in"\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
)

include\_directories("${PROJECT\_BINARY\_DIR}/include")

## ARM64 CRC32C code is built separately, so we don't accidentally compile

## unsupported instructions into code that gets run without ARM32 support.

add\_library(crc32c\_arm64 OBJECT "")\
target\_sources(crc32c\_arm64\
PRIVATE\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
"src/crc32c\_arm64.cc"\
"src/crc32c\_arm64.h"\
)\
if(HAVE\_ARM64\_CRC32C)\
if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
\# TODO(pwnall): Insert correct flag when VS gets ARM64 CRC32C support.\
target\_compile\_options(crc32c\_arm64 PRIVATE "/arch:NOTYET")\
else(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
target\_compile\_options(crc32c\_arm64 PRIVATE "-march=armv8-a+crc+crypto")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
endif(HAVE\_ARM64\_CRC32C)

## CMake only enables PIC by default in SHARED and MODULE targets.

if(BUILD\_SHARED\_LIBS)\
set\_property(TARGET crc32c\_arm64 PROPERTY POSITION\_INDEPENDENT\_CODE TRUE)\
endif(BUILD\_SHARED\_LIBS)

## SSE4.2 code is built separately, so we don't accidentally compile unsupported

## instructions into code that gets run without SSE4.2 support.

add\_library(crc32c\_sse42 OBJECT "")\
target\_sources(crc32c\_sse42\
PRIVATE\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
"src/crc32c\_sse42.cc"\
"src/crc32c\_sse42.h"\
)\
if(HAVE\_SSE42)\
if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
target\_compile\_options(crc32c\_sse42 PRIVATE "/arch:AVX")\
else(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
target\_compile\_options(crc32c\_sse42 PRIVATE "-msse4.2")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
endif(HAVE\_SSE42)

## CMake only enables PIC by default in SHARED and MODULE targets.

if(BUILD\_SHARED\_LIBS)\
set\_property(TARGET crc32c\_sse42 PROPERTY POSITION\_INDEPENDENT\_CODE TRUE)\
endif(BUILD\_SHARED\_LIBS)

## Must be included before CMAKE\_INSTALL\_INCLUDEDIR is used.

include(GNUInstallDirs)

add\_library(crc32c ""

## TODO(pwnall): Move the TARGET\_OBJECTS generator expressions to the PRIVATE

## section of target\_sources when cmake\_minimum\_required becomes 3.9 or above.

$\<TARGET\_OBJECTS:crc32c\_arm64>\
$\<TARGET\_OBJECTS:crc32c\_sse42>\
)\
target\_sources(crc32c\
PRIVATE\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
"src/crc32c\_arm64.h"\
"src/crc32c\_arm64\_check.h"\
"src/crc32c\_internal.h"\
"src/crc32c\_portable.cc"\
"src/crc32c\_prefetch.h"\
"src/crc32c\_read\_le.h"\
"src/crc32c\_round\_up.h"\
"src/crc32c\_sse42.h"\
"src/crc32c\_sse42\_check.h"\
"src/crc32c.cc"

## Only CMake 3.3+ supports PUBLIC sources in targets exported by "install".

$<$\<VERSION\_GREATER:CMAKE\_VERSION,3.2>:PUBLIC>\
"include/crc32c/crc32c.h"\
)

target\_include\_directories(crc32c\
PUBLIC\
$\<BUILD\_INTERFACE:${PROJECT\_SOURCE\_DIR}/include>\
$\<INSTALL\_INTERFACE:${CMAKE\_INSTALL\_INCLUDEDIR}>\
)

set\_property(\
TARGET crc32c\_arm64 crc32c\_sse42 crc32c\
APPEND\
PROPERTY COMPILE\_DEFINITIONS CRC32C\_HAVE\_CONFIG\_H\
)

set\_target\_properties(crc32c\
PROPERTIES VERSION ${PROJECT\_VERSION} SOVERSION ${PROJECT\_VERSION\_MAJOR})

## Warnings as errors in Visual Studio for this project's targets.

if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set\_property(TARGET crc32c APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
set\_property(TARGET crc32c\_arm64 APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
set\_property(TARGET crc32c\_sse42 APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

if(CRC32C\_BUILD\_TESTS)\
enable\_testing()

## Prevent overriding the parent project's compiler/linker settings on Windows.

set(gtest\_force\_shared\_crt ON CACHE BOOL "" FORCE)\
set(install\_gtest OFF)\
set(install\_gmock OFF)

## This project is tested using GoogleTest.

add\_subdirectory("third\_party/googletest")

## GoogleTest triggers a missing field initializers warning.

if(CRC32C\_HAVE\_NO\_MISSING\_FIELD\_INITIALIZERS)\
set\_property(TARGET gtest\
APPEND PROPERTY COMPILE\_OPTIONS -Wno-missing-field-initializers)\
set\_property(TARGET gmock\
APPEND PROPERTY COMPILE\_OPTIONS -Wno-missing-field-initializers)\
endif(CRC32C\_HAVE\_NO\_MISSING\_FIELD\_INITIALIZERS)

add\_executable(crc32c\_tests "")\
target\_sources(crc32c\_tests\
PRIVATE\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
"src/crc32c\_arm64\_unittest.cc"\
"src/crc32c\_extend\_unittests.h"\
"src/crc32c\_portable\_unittest.cc"\
"src/crc32c\_prefetch\_unittest.cc"\
"src/crc32c\_read\_le\_unittest.cc"\
"src/crc32c\_round\_up\_unittest.cc"\
"src/crc32c\_sse42\_unittest.cc"\
"src/crc32c\_unittest.cc"\
"src/crc32c\_test\_main.cc"\
)\
target\_link\_libraries(crc32c\_tests crc32c gtest)

## Warnings as errors in Visual Studio for this project's targets.

if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set\_property(TARGET crc32c\_tests APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

if(CRC32C\_USE\_GLOG)\
target\_link\_libraries(crc32c\_tests glog)\
endif(CRC32C\_USE\_GLOG)

add\_test(NAME crc32c\_tests COMMAND crc32c\_tests)

add\_executable(crc32c\_capi\_tests "")\
target\_sources(crc32c\_capi\_tests\
PRIVATE\
"src/crc32c\_capi\_unittest.c"\
)\
target\_link\_libraries(crc32c\_capi\_tests crc32c)

## Warnings as errors in Visual Studio for this project's targets.

if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set\_property(TARGET crc32c\_capi\_tests APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")

add\_test(NAME crc32c\_capi\_tests COMMAND crc32c\_capi\_tests)\
endif(CRC32C\_BUILD\_TESTS)

if(CRC32C\_BUILD\_BENCHMARKS)\
add\_executable(crc32c\_bench "")\
target\_sources(crc32c\_bench\
PRIVATE\
"${PROJECT\_BINARY\_DIR}/include/crc32c/crc32c\_config.h"\
"src/crc32c\_benchmark.cc"\
)\
target\_link\_libraries(crc32c\_bench crc32c)

## This project uses Google benchmark for benchmarking.

set(BENCHMARK\_ENABLE\_TESTING OFF CACHE BOOL "" FORCE)\
set(BENCHMARK\_ENABLE\_EXCEPTIONS OFF CACHE BOOL "" FORCE)\
add\_subdirectory("third\_party/benchmark")\
target\_link\_libraries(crc32c\_bench benchmark)

if(CRC32C\_USE\_GLOG)\
target\_link\_libraries(crc32c\_bench glog)\
endif(CRC32C\_USE\_GLOG)

## Warnings as errors in Visual Studio for this project's targets.

if(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
set\_property(TARGET crc32c\_bench APPEND PROPERTY COMPILE\_OPTIONS "/WX")\
endif(CMAKE\_CXX\_COMPILER\_ID STREQUAL "MSVC")\
endif(CRC32C\_BUILD\_BENCHMARKS)

if(CRC32C\_INSTALL)\
install(TARGETS crc32c\
EXPORT Crc32cTargets\
RUNTIME DESTINATION ${CMAKE\_INSTALL\_BINDIR}\
LIBRARY DESTINATION ${CMAKE\_INSTALL\_LIBDIR}\
ARCHIVE DESTINATION ${CMAKE\_INSTALL\_LIBDIR}\
)\
install(\
FILES\
"include/crc32c/crc32c.h"\
DESTINATION "${CMAKE\_INSTALL\_INCLUDEDIR}/crc32c"\
)

include(CMakePackageConfigHelpers)\
configure\_package\_config\_file(\
"${PROJECT\_NAME}Config.cmake.in"\
"${PROJECT\_BINARY\_DIR}/${PROJECT\_NAME}Config.cmake"\
INSTALL\_DESTINATION "${CMAKE\_INSTALL\_LIBDIR}/cmake/${PROJECT\_NAME}"\
)\
write\_basic\_package\_version\_file(\
"${PROJECT\_BINARY\_DIR}/${PROJECT\_NAME}ConfigVersion.cmake"\
COMPATIBILITY SameMajorVersion\
)\
install(\
EXPORT Crc32cTargets\
NAMESPACE Crc32c::\
DESTINATION "${CMAKE\_INSTALL\_LIBDIR}/cmake/${PROJECT\_NAME}"\
)\
install(\
FILES\
"${PROJECT\_BINARY\_DIR}/${PROJECT\_NAME}Config.cmake"\
"${PROJECT\_BINARY\_DIR}/${PROJECT\_NAME}ConfigVersion.cmake"\
DESTINATION "${CMAKE\_INSTALL\_LIBDIR}/cmake/${PROJECT\_NAME}"\
)\
endif(CRC32C\_INSTALL)
