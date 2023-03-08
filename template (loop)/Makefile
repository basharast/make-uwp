platform = win
system_platform = win	
PlatformSuffix = uwp
TargetArch = arm
	
#Library
TARGET_NAME := libraryname
STATIC_LINKING = 0
DEBUG = 0

#Locations
RootDir = .
VsInstallCommunity = C:\Program Files\Microsoft Visual Studio\2022\Preview
WindowsSdkDir := C:\Program Files (x86)\Windows Kits\10

#Build
WindowsSDKVersion := 10.0.14393.0

#Custom includes
INCFLAGS += -I"$(RootDir)/custom-includes"
#Custom libraries
LIBS+= "$(RootDir)/libs/library.lib"
#Custom flags
MSVCCompileFlags += -DCUSTOM_DEFINE_HERE -D_ARM_ -D_M_ARM_ 

#--------------------------------------------------
#Includes
INCFLAGS += -I"$(RootDir)"

#Flags
MSVCCompileFlags += -DWINAPI_FAMILY=WINAPI_FAMILY_APP -D_WINDLL -D_UNICODE -DUNICODE -D__WRL_NO_DEFAULT_LIB__ -EHsc -FS 

ifeq ($(STATIC_LINKING), 1)
LDFLAGS += -NOLOGO 
else
LDFLAGS += -APPCONTAINER -NXCOMPAT -DYNAMICBASE -MANIFEST:NO -LTCG -OPT:REF -SUBSYSTEM:CONSOLE -MANIFESTUAC:NO -OPT:ICF -ERRORREPORT:PROMPT -NOLOGO -TLBID:1 -WINMD:NO
endif

#Arch flags
MSVCCompileFlags += -D_WIN32 -D_WIN32_ -DWIN32 -DOS_WINDOWS -DNOMINMAX

CFLAGS += $(MSVCCompileFlags)
CXXFLAGS += $(MSVCCompileFlags)

#Libs
ifneq ($(STATIC_LINKING), 1)
LIBS += WindowsApp.lib
endif

#Tools
ifeq ($(STATIC_LINKING), 1)
CC  = cl.exe -c
CXX = cl.exe -c
else
CC  = cl.exe
CXX = cl.exe
endif

LD = link.exe
LS = lib.exe

#Includes
VsInstallRoot := $(VsInstallCommunity)

VcCompilerToolsVer := $(shell cat "$(VsInstallRoot)/VC/Auxiliary/Build/Microsoft.VCToolsVersion.default.txt" | grep -o '[0-9\.]*')
VcCompilerToolsDir := $(VsInstallRoot)/VC/Tools/MSVC/$(VcCompilerToolsVer)

WindowsSDKSharedIncludeDir = -I"$(WindowsSdkDir)\Include\$(WindowsSDKVersion)\shared"
WindowsSDKUCRTIncludeDir = -I"$(WindowsSdkDir)\Include\$(WindowsSDKVersion)\ucrt"
WindowsSDKUMIncludeDir = -I"$(WindowsSdkDir)\Include\$(WindowsSDKVersion)\um"
WindowsSDKUCRTLibDir = -LIBPATH:"$(WindowsSdkDir)\Lib\$(WindowsSDKVersion)\ucrt\$(TargetArch)"
WindowsSDKUMLibDir = -LIBPATH:"$(WindowsSdkDir)\Lib\$(WindowsSDKVersion)\um\$(TargetArch)"

VCCompilerToolsBinDir := $(VcCompilerToolsDir)\bin\HostX64

PATH := $(shell IFS=$$'\n'; cygpath "$(VCCompilerToolsBinDir)/$(TargetArch)"):$(PATH)
PATH := $(PATH):$(shell IFS=$$'\n'; cygpath "$(VsInstallRoot)/Common7/IDE")
INCLUDE := $(shell IFS=$$'\n'; cygpath -w "$(VcCompilerToolsDir)/include")
LIB := $(shell IFS=$$'\n'; cygpath -w "$(VcCompilerToolsDir)/lib/$(TargetArch)")

LIB_STORE= $(shell IFS=$$'\n'; cygpath -w "$(LIB)/store")

ifeq ($(STATIC_LINKING), 1)
LDFLAGS  +=  -LIBPATH:"$(LIB)"
else
LDFLAGS  +=  -LIBPATH:"$(LIB_STORE)"
endif

INCFLAGS +=  -I"$(INCLUDE)" $(WindowsSDKSharedIncludeDir) $(WindowsSDKUCRTIncludeDir) $(WindowsSDKUMIncludeDir)
LDFLAGS  +=  -LIBPATH:"$(LIB_STORE)" -LIBPATH:"$(LIB)" $(WindowsSDKUCRTLibDir) $(WindowsSDKUMLibDir)


ifeq ($(STATIC_LINKING), 1)
TARGET := $(TARGET_NAME).lib
else
TARGET := $(TARGET_NAME).dll
endif

#C/C++ files
include Makefile.common

CFLAGS += -D_CRT_SECURE_NO_WARNINGS
CXXFLAGS += -D_CRT_SECURE_NO_WARNINGS

OBJECTS     += $(SOURCES_CXX:.cpp=.o) $(SOURCES_C:.c=.o) $(SOURCES_ASM:.S=.o) $(SOURCES_NASM:.asm=.o)
CXXFLAGS    += $(INCFLAGS)
CFLAGS      += $(INCFLAGS)

#Debug flags
ifeq ($(DEBUG), 1)
     ifeq ($(STATIC_LINKING),1)
         CFLAGS += -MTd
         CXXFLAGS += -MTd
     else
         CFLAGS += -MDd
         CXXFLAGS += -MDd
     endif
     CFLAGS += -Od -Zi -DDEBUG -D_DEBUG
     CXXFLAGS += -Od -Zi -DDEBUG -D_DEBUG
else
     ifeq ($(STATIC_LINKING),1)
         CFLAGS += -MT
         CXXFLAGS += -MT
     else
         CFLAGS += -MD
         CXXFLAGS += -MD
     endif
     CFLAGS += -O2 -DNDEBUG
     CXXFLAGS += -O2 -DNDEBUG
endif

#Tools params
OBJOUT = -Fo
LINKOUT = -out:

#Make
all: $(TARGET)

BUILDDIR = $(TargetArch)
$(info Building $(TARGET) with $(TargetArch) configuration (obj files stored in build/$(BUILDDIR)) ...)

SOURCES_CXX := $(wildcard $(SOURCES_CXX))
SOURCES_C := $(wildcard $(SOURCES_C))

$(if $(findstring ~,$(SOURCES_CXX)),$(error SOURCES_CXX contains a filename with a ~ character in it - Unable to continue))
$(if $(findstring ~,$(SOURCES_C)),$(error SOURCES_C contains a filename with a ~ character in it - Unable to continue))
$(if $(wildcard build),,$(shell mkdir "build"))
$(if $(wildcard build/$(BUILDDIR)),,$(shell mkdir "build/$(BUILDDIR)"))
OBJECTS := $(addprefix build/$(BUILDDIR)/,$(subst /,~,$(patsubst %,%.o,$(SOURCES_CXX)))) $(addprefix build/$(BUILDDIR)/,$(subst /,~,$(patsubst %,%.o,$(SOURCES_C))))
-include $(OBJECTS:%.o=%.d)
$(foreach F,$(OBJECTS),$(eval $(F): $(subst ~,/,$(patsubst build/$(BUILDDIR)/%.o,%,$(F))) ; $$(call COMPILE,$$@,$$<)))

$(TARGET): $(OBJECTS)

ifeq ($(STATIC_LINKING), 1)
	$(LS) $(LDFLAGS) $(LINKOUT)$@ $(OBJECTS) $(LIBS)
else
	$(LD) $(LDFLAGS) $(LINKOUT)$@ $(OBJECTS) $(LIBS)
endif

define COMPILE
     $(CXX) $(CXXFLAGS) -c $(OBJOUT)$1 $2
endef

clean:
	find $(ROOT_DIR) -name "*.o" -type f -delete
	find $(ROOT_DIR) -name "*.a" -type f -delete
	rm -f $(OBJECTS) $(TARGET) $(TARGET_NAME).lib $(TARGET_NAME).exp

.PHONY: clean
