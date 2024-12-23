# CMakeList.txt : CMake project for dialogc, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)

# Enable Hot Reload for MSVC compilers if supported.
if (POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

add_definitions("-DVERSION=\"0m/03\"")

project ("dialogc")

# Add source to this project's executable.
add_executable (dialogc "src/aavm.c"
						"src/accesspred.c"
						"src/arena.c"
						"src/ast.c"
						"src/backend.c"
						"src/backend_aa.c"
						"src/backend_z.c"
						"src/blorb.c"
						"src/compile.c"
						"src/crc32.c"
						"src/dumb_output.c"
						"src/dumb_report.c"
						"src/eval.c"
						"src/frontend.c"
						"src/parse.c"
						"src/runtime_z.c"
						"src/unicode.c"
)

# dgdebug.exe is only 32-bit, to compile it
#   1. Start MSYS2 MINGW32 with mingw32.exe
#   2. Install 32-bit MinGW: pacman -S mingw-w64-i686-gcc
#   3. make dgdebug.exe` in the Dialog /src directory
# To run dgdebug you need Glk.dll and ScaleGfx.dll in same directory. 

if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET dialogc PROPERTY CXX_STANDARD 20)
endif()

# TODO: Add tests and install targets if needed.
