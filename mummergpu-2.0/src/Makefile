
all: clean mummergpu
.SUFFIXES : .cu .cu_dbg_o .c_dbg_o .cpp_dbg_o .cu_rel_o .c_rel_o .cpp_rel_o .cubin

## Reenable this for testing all variations
#include ../experiments/test_rule.mk

CUDA_INSTALL_PATH := /usr/local/cuda

# Compilers
NVCC       := $(CUDA_INSTALL_PATH)/bin/nvcc 
CXX        := g++ $(PROFILE)
CC         := gcc 
LINK       := g++ $(PROFILE)

# Add source files here
STATIC_LIB := libmummergpu.a
# Cuda source files (compiled with cudacc)
CUFILES		:= mummergpu.cu
# C/C++ source files (compiled with gcc / c++)
CCFILES		:= \
	 mummergpu_gold.cpp suffix-tree.cpp PoolMalloc.cpp

################################################################################
# Rules and targets


# Basic directory setup for SDK
# (override directories only if they are not already defined)
SRCDIR     ?= 
ROOTDIR    ?= ..
ROOTBINDIR ?= $(ROOTDIR)/bin
BINDIR     ?= $(ROOTBINDIR)
ROOTOBJDIR ?= $(ROOTDIR)/obj
LIBDIR     := $(ROOTDIR)/lib
BINNAME    ?= mummergpu
INCLUDES  += -I. -I$(CUDA_INSTALL_PATH)/include 
LIB       := -L$(CUDA_INSTALL_PATH)/lib64 -L$(LIBDIR) -lcudart -lcuda ${LIB}

# Warning flags
CXXWARN_FLAGS := \
	-W -Wall \
	-Wswitch \
	-Wformat \
	-Wchar-subscripts \
	-Wparentheses \
	-Wmultichar \
	-Wtrigraphs \
	-Wpointer-arith \
	-Wcast-align \
	-Wreturn-type \
	-Wno-unused-function \
	$(SPACE)

CWARN_FLAGS := $(CXXWARN_FLAGS) \
	-Wstrict-prototypes \
	-Wmissing-prototypes \
	-Wmissing-declarations \
	-Wnested-externs \
	-Wmain \

# Compiler-specific flags
NVCCFLAGS := 
CXXFLAGS  := $(CXXWARN_FLAGS)
CFLAGS    := $(CWARN_FLAGS)

# Common flags
COMMONFLAGS ?= 
COMMONFLAGS += $(INCLUDES) -DUNIX

# Debug/release configuration
ifeq ($(dbg),1)
	COMMONFLAGS += -g
	NVCCFLAGS   += -D_DEBUG
	BINSUBDIR   := debug
	LIBSUFFIX   := D
else 
	COMMONFLAGS += -O3
	BINSUBDIR   := release
	LIBSUFFIX   :=
endif

# Device emulation configuration
ifeq ($(emu), 1)
	NVCCFLAGS   += -deviceemu
	CUDACCFLAGS += 
	BINSUBDIR   := emu$(BINSUBDIR)
endif

# Add cudacc flags
NVCCFLAGS += -Xcompiler "$(CUDACCFLAGS)"

# Add common flags
NVCCFLAGS += $(COMMONFLAGS)
CXXFLAGS  += $(COMMONFLAGS)
CFLAGS    += $(COMMONFLAGS)

OBJDIR := $(ROOTOBJDIR)/$(BINSUBDIR)

#.c.c_$(APPEND)_o:
$(OBJDIR)/%.c_o : $(SRCDIR)%.c
	$(CC) $(CFLAGS) -o $@ -c $<

#.cpp.cpp_$(APPEND)_o:
$(OBJDIR)/%.cpp_o : $(SRCDIR)%.cpp
	$(CXX) $(CXXFLAGS) -o $@ -c $<

#.cu.cu_$(APPEND)_o:
$(OBJDIR)/%.cu_o : $(SRCDIR)%.cu
	$(NVCC) -o $@ -c $< $(NVCCFLAGS)

#.cu.cubin
%.cubin : $(SRCDIR)mummergpu.cu mummergpu_kernel.cu
	$(NVCC) -o $(BINNAME).cubin -cubin $< $(NVCCFLAGS)

#.cu.ptx
%.ptx : $(SRCDIR)%.cu mummergpu_kernel.cu
	$(NVCC) -o $@ -ptx -Xopencc -LIST:source=on $< $(NVCCFLAGS)

makedirectories:
	@mkdir -p $(LIBDIR)
	@mkdir -p $(OBJDIR)
	@mkdir -p $(BINDIR)/$(BINSUBDIR)

LIBOBJS= \
    $(OBJDIR)/mummergpu_gold.cpp_o \
    $(OBJDIR)/suffix-tree.cpp_o \
    $(OBJDIR)/PoolMalloc.cpp_o \
    $(OBJDIR)/mummergpu.cu_o \

$(LIBDIR)/libmummergpu$(LIBSUFFIX).a: $(LIBOBJS)
	ar qv $@ $(LIBOBJS)

BINOBJS= \
    $(OBJDIR)/mummergpu_main.cpp_o

$(BINDIR)/$(BINSUBDIR)/mummergpu: makedirectories $(LIBDIR)/libmummergpu$(LIBSUFFIX).a $(BINOBJS)
	$(LINK) -o $(BINDIR)/$(BINSUBDIR)/$(BINNAME)  $(BINOBJS) -lmummergpu$(LIBSUFFIX) $(LIB) 
		

##############################################################################
#                               Auto Generated rules go here                 #
##############################################################################

## Reenable this for testing all variants
#include ../experiments/rules.mk

##############################################################################
#                              End auto generated rules                      #
##############################################################################


clean: 
	rm -f $(LIBOBJS)
	rm -f $(LIBDIR)/libmummergpu$(LIBSUFFIX).a
	rm -f $(BINOBJS)
	rm -f $(BINDIR)/$(BINSUBDIR)/$(BINNAME)

really-clean: clean
	rm -rf $(ROOTBINDIR)
	rm -rf $(ROOTOBJDIR)
	rm -rf $(LIBDIR)

mummergpu: $(BINDIR)/$(BINSUBDIR)/mummergpu

cubin: mummergpu.cubin

ptx: mummergpu.ptx

