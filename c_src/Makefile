CC ?= cc
CFLAGS = -DHAVE_SETNS -g -Wall -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0
LDFLAGS = -L.
LDLIBS = -lancillary

# Detect architecture
UNAME_M := $(shell uname -m)
ifeq ($(UNAME_M),x86_64)
    # x86_64 needs -m64 flag
    CFLAGS += -m64
    ARCH_FLAG = -m64
else ifeq ($(UNAME_M),aarch64)
    # ARM doesn't need -m64
    ARCH_FLAG =
else ifeq ($(findstring arm,$(UNAME_M)),arm)
    # Other ARM variants
    ARCH_FLAG =
else
    # Default for unknown
    ARCH_FLAG =
endif

# Erlang paths - auto-detect
ERL_ROOT_DIR = $(shell erl -eval "io:format(\"~s\", [code:root_dir()])" -s init stop -noshell 2>/dev/null)
ifeq ($(ERL_ROOT_DIR),)
    # Fallback for when erl fails
    ERL_ROOT_DIR = /usr/lib/erlang
endif

# Find erts include directory
ERTS_VERSIONS = $(wildcard $(ERL_ROOT_DIR)/erts-*/include)
ifneq ($(ERTS_VERSIONS),)
    # Use the first (likely highest) erts version found
    ERL_EI_INCLUDE_DIR = $(firstword $(ERTS_VERSIONS))
else
    # Fallback
    ERL_EI_INCLUDE_DIR = $(ERL_ROOT_DIR)/usr/include
endif

ERL_CFLAGS = -O3 -std=c99 -finline-functions -Wall -Wmissing-prototypes -fPIC $(ARCH_FLAG) -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0

all: ancillary cmd nif

ancillary:
	$(MAKE) -f Makefile.ancillary ARCH_FLAG="$(ARCH_FLAG)"

cmd: ancillary
	$(CC) $(CFLAGS) $(ARCH_FLAG) -o ../priv/procket -L. procket_cmd.c $(LDLIBS)

nif: ancillary
	$(CC) $(ERL_CFLAGS) -I $(ERL_EI_INCLUDE_DIR) -c -o procket.o procket.c
	$(CC) procket.o -shared -L. $(LDLIBS) -o ../priv/procket.so

clean:
	rm -f *.o *.a ../priv/procket ../priv/procket.so
	$(MAKE) -f Makefile.ancillary clean

.PHONY: all ancillary cmd nif clean