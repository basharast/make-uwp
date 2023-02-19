# Makefile Template (UWP)
If you are new to compiling using makefile's and you want to compile something to support (UWP), 

then this simple template will help you to understand how the progress is working

It's organized and easy to change.


# Tools

- [Visual Studio](https://visualstudio.microsoft.com/vs/community/) (with MSVC C++ build tools)
- [MSYS2](https://www.msys2.org/)
- [Windows SDK](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive/)

# Usage
In `Makefile` you will find:

- `TargetArch` your target arch
- `TARGET_NAME` the name of `dll` or `lib` (without extension)
- `STATIC_LINKING` if you want the output as `lib`
- `VsInstallCommunity` VS installation dir
- `WindowsSdkDir` SDK dir
- `WindowsSDKVersion` Target build number
- `INCFLAGS` include folders `-I"$(RootDir)/foldername"` or `-I"fullpath"` [always use `/`]
- `LIBS` libraries to include [for linker]
- `MSVCCompileFlags` custom defines `-DVARIABLENAME` [`-D` is fixed at the start, means `define`]

## Add c/c++ files

- Open `Makefile.common`
- `SOURCES_C` for `.c` files
- `SOURCES_CXX` for `.cpp` files

**Example:**

```
SOURCES_C += \
            file1.c \
	    file2.c \
	    file3.c
			
SOURCES_CXX += \
            file1.cpp \
	    file2.cpp \
	    file3.cpp
```


## Build

From msys2:
- `cd "fullpath_to_library"`
- `make -f Makefile`


## Clean
- `cd "fullpath_to_library"`
- `make -f Makefile clean`
