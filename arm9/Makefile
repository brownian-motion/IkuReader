#-----------------------------------------------------------------------
.SUFFIXES:
#-----------------------------------------------------------------------

ifeq ($(strip $(DEVKITARM)),)
$(error "DEVKITARM not in environment.")
endif

include $(DEVKITARM)/ds_rules

#-----------------------------------------------------------------------
# TARGET is the name of the output
# BUILD is the directory where object files & intermediate files will be placed
# SOURCES is a list of directories containing source code
# INCLUDES is a list of directories containing extra header files
#-----------------------------------------------------------------------
TARGET		:=	iku
BUILD		:=	build
SOURCES		:=	source source/lib
INCLUDES	:=	../include ../include/freetype2 ../include/utf8 ../include/pugi ../include/zip
DATA		:=	../data

#-----------------------------------------------------------------------
# options for code generation
#-----------------------------------------------------------------------
#ARCH	:=	-mthumb -mthumb-interwork -march=armv5te -mtune=arm946e-s 
ARCH	:=	-mthumb -mthumb-interwork -mcpu=arm9tdmi -mtune=arm9tdmi 

CFLAGS	:=	-Wall -fomit-frame-pointer \
		-ffast-math $(ARCH) $(INCLUDE) -DARM9 -Os

CXXFLAGS	:= $(CFLAGS) -fno-rtti -fno-exceptions 

ASFLAGS	:=	-g $(ARCH)
LDFLAGS	=	-specs=ds_arm9.specs -g $(ARCH) -mno-fpu \
		-Wl,-Map,$(notdir $*.map)

#-----------------------------------------------------------------------
# any extra libraries we wish to link with the project
#-----------------------------------------------------------------------
LIBS	:= -lz -lfreetype -lfat -lnds9

#-----------------------------------------------------------------------
# list of directories containing libraries, this must be the top level
# containing include and lib
#-----------------------------------------------------------------------
LIBDIRS	:=	 $(LIBNDS) $(CURDIR)/..
#-----------------------------------------------------------------------
# no real need to edit anything past this point unless you need to add
# additional rules for different file extensions
#-----------------------------------------------------------------------
ifneq ($(BUILD),$(notdir $(CURDIR)))
#-----------------------------------------------------------------------

export OUTPUT	:=	$(CURDIR)/$(TARGET)

export VPATH	:=	$(foreach dir,$(SOURCES),$(CURDIR)/$(dir)) \
					$(foreach dir,$(DATA),$(CURDIR)/$(dir))

export DEPSDIR	:=	$(CURDIR)/$(BUILD)

CFILES		:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.c)))
CPPFILES	:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.cpp)))
SFILES		:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.s)))
BINFILES	:=	$(foreach dir,$(DATA),$(notdir $(wildcard $(dir)/*.bin)))
PNGFILES	:=	$(foreach dir,$(DATA),$(notdir $(wildcard $(dir)/*.png)))

#-----------------------------------------------------------------------
# use CXX for linking C++ projects, CC for standard C
#-----------------------------------------------------------------------
ifeq ($(strip $(CPPFILES)),)
#-----------------------------------------------------------------------
	export LD	:=	$(CC)
#-----------------------------------------------------------------------
else
#-----------------------------------------------------------------------
	export LD	:=	$(CXX)
#-----------------------------------------------------------------------
endif
#-----------------------------------------------------------------------

export OFILES	:=	$(addsuffix .o,$(BINFILES)) \
			$(PNGFILES:.png=.o) \
			$(CPPFILES:.cpp=.o) $(CFILES:.c=.o) $(SFILES:.s=.o)

export INCLUDE	:=	$(foreach dir,$(INCLUDES),-I$(CURDIR)/$(dir)) \
			$(foreach dir,$(LIBDIRS),-I$(dir)/include -I$(dir)/include/nds) \
			$(foreach dir,$(LIBDIRS),-I$(dir)/lib) \
			-I$(CURDIR)/$(BUILD)

export LIBPATHS	:=	$(foreach dir,$(LIBDIRS),-L$(dir)/lib)

.PHONY: $(BUILD) export dldi test install

all: $(BUILD)
#-----------------------------------------------------------------------
$(BUILD):
	@[ -d $@ ] || mkdir -p $@
	@make --no-print-directory -C $(BUILD) -f $(CURDIR)/Makefile

#-----------------------------------------------------------------------
clean:
	@echo clean ...
	@rm -fr $(BUILD)
	@rm -fr $(TARGET).arm9.elf $(TARGET).arm9

#-----------------------------------------------------------------------
else

DEPENDS	:=	$(OFILES:.o=.d)

#-----------------------------------------------------------------------
# main targets
#-----------------------------------------------------------------------
$(OUTPUT).arm9	:	$(OUTPUT).arm9.elf
$(OUTPUT).arm9.elf	:	$(OFILES)

#-----------------------------------------------------------------------
%.o	:	%.bin
#-----------------------------------------------------------------------
	@echo $(notdir $<)
	$(bin2o)

-include $(DEPENDS)

#-----------------------------------------------------------------------------
endif
#-----------------------------------------------------------------------------
