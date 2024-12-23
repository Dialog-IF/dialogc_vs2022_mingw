## Setting up Dialog to compile in Visual Studio 2022 with minGW64

1. Install *MSYS2* in `C:\msys64` (https://www.msys2.org/). Use default settings for installation.

2. Open a *MSYS2* (any) terminal and install gcc toolchain for both ucrt64 and mingw64.

```
    pacman -S mingw-w64-ucrt-x86_64-gcc
    pacman -S mingw-w64-x86_64-gcc
    pacman -S mingw-w64-i686-gcc
    pacman -S make
```

3. Use the *MSYS2 MINGW64* terminal if you want to compile and link with `MSVCRT` for C (compatible with pre Win10 but lacks some features). Use *MSYS2 UCRT64* terminal if you want to compile and link with `UCRT` for C (Win10+, more "modern", UTF8). See https://www.msys2.org/docs/environments/ for more details.

4. To use `gcc` and Linux style commands in Windows CMD or PowerShell windows, add `C:\msys64\ucrt64\bin` and `C:\msys64\usr\bin` to the path environment variable (*Settings/System/Advanced/Environment Variables*).

5. Create new *CMake* project in *Visual Studio 2022* (check box for `place solution and project in same directory`).

6. Delete the dummy files `*.cpp` and `*.h` that was created in the new project.

7. Copy the *Dialog* `\src` directory to the newly created project.

8. Add configurations for *Mingw64* and *Ucrt64* to `CMakePresets.json`.
```
    {
      "name": "Mingw64-debug",
      "displayName": "Mingw64 Debug",
      "inherits": "windows-base",
      "environment": {
        "PATH": "C:/msys64/mingw64/bin;$penv{PATH}"
      },
      "cacheVariables": {
        "CMAKE_C_COMPILER": "gcc.exe",
        "CMAKE_CXX_COMPILER": "g++.exe",
        "CMAKE_BUILD_TYPE": "Debug"
      }
    },
    {
      "name": "Mingw64-release",
      "displayName": "Mingw64 Release",
      "inherits": "Mingw64-debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    },
    {
      "name": "Ucrt64-debug",
      "displayName": "Ucrt64 Debug",
      "inherits": "windows-base",
      "environment": {
        "PATH": "C:/msys64/ucrt64/bin;$penv{PATH}"
      },
      "cacheVariables": {
        "CMAKE_C_COMPILER": "gcc.exe",
        "CMAKE_CXX_COMPILER": "g++.exe",
        "CMAKE_BUILD_TYPE": "Debug"
      }
    },
    {
      "name": "Ucrt64-release",
      "displayName": "Ucrt64 Release",
      "inherits": "Ucrt64-debug",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
```

9. Change `CMakeList.txt` (`add_definitions` and `add_executable` are new and/or changed from the default).
```
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
	
	if (CMAKE_VERSION VERSION_GREATER 3.12)
	  set_property(TARGET dialogc PROPERTY CXX_STANDARD 20)
	endif()
	
	# TODO: Add tests and install targets if needed.
```

10. Compile and debug for *Mingw64* or *Ucrt64* should now work. Arguments for debugging can be added under menu *Debug/Debug and Launch Settings for \<project>*.
```
  {
    "version": "0.2.1",
    "defaults": {},
    "configurations": [
      {
        "type": "default",
        "project": "CMakeLists.txt",
        "projectTarget": "dialogc.exe",
        "name": "dialogc.exe",
        "args": [
          "--version"
        ]
      }
    ]
  }
```



The debugger won't run in VS2022 yet, but if you want to compile it in *MSYS2* you need to:

1. Install mingw32 gcc
```
    pacman -S mingw-w64-i686-gcc
```
2. Start the *MSYS2 MINGW32* environment with `mingw32.exe`.

3. `make dgdebug.exe` in the *Dialog* `/src`.

To run *dgdebug* you need `Glk.dll` and `ScaleGfx.dll` in same directory as the `dgdebug.exe`.
