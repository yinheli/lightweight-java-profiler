SHELL:=/bin/bash
JAVA_HOME := $(shell \
	[[ -n "$${JAVA_HOME}" ]] || \
	  JAVA_HOME=$$(dirname $$(readlink -f $$(which java)))/../; \
	[[ "$${JAVA_HOME}" =~ /jre/ ]] && JAVA_HOME=$${JAVA_HOME}/../; \
	[[ -n "$${JAVA_HOME}" ]] || (echo "Cannot find JAVA_HOME" && exit) ; \
	echo $${JAVA_HOME})
CC=g++
AGENT=liblagent.so
LIBS=-ldl
BITS?=32
BUILD_DIR ?= $(shell mkdir build-$(BITS) 2> /dev/null ; echo build-$(BITS))
SRC_DIR:=${PWD}/src
OPT?=-O2
WARNINGS=-Wall -Werror -Wformat-security -Wframe-larger-than=16384 -Wno-char-subscripts -Wno-sign-compare -Wno-strict-overflow -Wno-unused-but-set-variable -Wunused-but-set-parameter -Wwrite-strings -Wvla -Wno-conversion-null -Wnon-virtual-dtor -Woverloaded-virtual -Wno-builtin-macro-redefined
OTHER_CFLAGS=-fdiagnostics-show-option -fno-exceptions -fno-omit-frame-pointer -fno-strict-aliasing -funsigned-char -fno-asynchronous-unwind-tables -m$(BITS) -mfpmath=sse -msse2 -g -std=gnu++0x -D__STDC_FORMAT_MACROS
INCLUDES=-I$(JAVA_HOME)/include -I$(JAVA_HOME)/include/linux

LDFLAGS=
LDFLAGS+=-Wl,--fatal-warnings 
# LDFLAGS+=-Wl,--export-dynamic-symbol=Agent_OnLoad 

SOURCES=$(wildcard $(SRC_DIR)/*.cc)
_OBJECTS=$(SOURCES:.cc=.pic.o)
OBJECTS = $(patsubst $(SRC_DIR)/%,$(BUILD_DIR)/%,$(_OBJECTS))

$(BUILD_DIR)/%.pic.o: $(SRC_DIR)/%.cc
	$(CC) $(INCLUDES) $(WARNINGS) $(OPT) $(OTHER_CFLAGS) -fvisibility=hidden -fPIC -c $< -o $@

$(AGENT): $(OBJECTS)
	$(CC) $(LDFLAGS) $(OTHER_CFLAGS) -shared -o $(BUILD_DIR)/$(AGENT) -Bsymbolic $(OBJECTS) $(LIBS)

all: $(AGENT)
clean:
	rm -rf $(BUILD_DIR)/*
