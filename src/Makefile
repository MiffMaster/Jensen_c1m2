#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Basic makefile for multi target build
#
# Use: make [Build Target] PLATOFRM=[Platform Override]
#
# Build Targets:
#      <FILE>.i - Preprocessed output file
#      <FILE>.o - Builds the <FILE>.o object file
#      <FILE>.asm - Assembly output files
#      build - Builds and links all source files
#      compile-all - Compile without linking
#      clean - Removes all generated files
#
# Platform Overrides:
#      MSP432 - Build for the embedded target device
#      HOST - Builds for testing on the host machine
#
#------------------------------------------------------------------------------
include sources.mk

# Platform common options
PROJECT = c1m2
LDFLAGS = -Wl,-Map=$(PROJECT).map
CFLAGS = -Wall -Werror -g -O0 -std=c99
CPPFLAGS = $(INCLUDES)

# Compiler flags and defines for MSP432
ifeq ($(PLATFORM),MSP432)
CPU = cortex-m4
ARCH = armv7e-m -mthumb
FPU = fpv4-sp-d16 -mfloat-abi=hard
SPECS = nosys.specs
LINKER_FILE = ../msp432p401r.lds
CC = arm-none-eabi-gcc
LD = arm-none-eabi-ld
SIZE = arm-none-eabi-size
OBJDUMP = arm-none-eabi-objdump
LDFLAGS := $(LDFLAGS) -T $(LINKER_FILE)
CFLAGS := $(CFLAGS) -mcpu=$(CPU) -march=$(ARCH) -mfpu=$(FPU) --specs=$(SPECS)
CPPFLAGS := $(INCLUDES) $(MSP_INCS)
SOURCES := $(SOURCES) $(MSP_SRCS)
# Compiler flags and defines for HOST
else ifeq ($(PLATFORM),HOST)
CC = gcc
LD = ld
SIZE = size
OBJDUMP = objdump
endif

# Pattern matche *.s files in SRCS and assigns *.o to OBJECTS
OBJECTS = $(SOURCES:.c=.o)

# Build targets
%.i: %.c
	$(CC) -E $< $(CPPFLAGS) $(CFLAGS) -D$(PLATFORM)  -o $@

c1m2.asm: $(PROJECT).out
	$(OBJDUMP) -D $(PROJECT).out > $@

%.asm: %.c
	$(CC) -S $< $(CPPFLAGS) $(CFLAGS) -D$(PLATFORM) -o $@

%.o: %.c
	$(CC) -c $< $(CPPFLAGS) $(CFLAGS) -D$(PLATFORM) -o $@
	
.PHONY: compile-all
compile-all: $(OBJECTS)

.PHONY: build
build: $(PROJECT).out
	$(SIZE) $(PROJECT).out

$(PROJECT).out : $(OBJECTS)
	$(CC) $(OBJECTS) $(CFLAGS) $(LDFLAGS) -o $@

.PHONY: clean
clean:
	rm -f *.i *.asm *.o $(PROJECT).out $(PROJECT).map

