CC ?= cc
CFLAGS = -DHAVE_SETNS -g -Wall -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0
LDFLAGS = -L.
LDLIBS = -lancillary

ERL_CFLAGS = -O3 -std=c99 -finline-functions -Wall -Wmissing-prototypes -fPIC -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0
ERL_EI_INCLUDE_DIR = /usr/lib/erlang/usr/include
ERL_ROOT_DIR = $(shell erl -eval "io:format(\"~s~n\", [code:root_dir()])" -s init stop -noshell)

ifeq ($(ERL_ROOT_DIR),)
   ERL_ROOT_DIR = /usr/lib/erlang
endif

ifeq ($(wildcard $(ERL_ROOT_DIR)/usr/include/*),)
   ERL_EI_INCLUDE_DIR = /usr/lib/erlang/erts-13.2.2.5/include
endif

all: ancillary cmd nif

ancillary:
	$(MAKE) -f Makefile.ancillary

cmd: ancillary
	$(CC) $(CFLAGS) -o ../priv/procket -L. procket_cmd.c $(LDLIBS)

nif: ancillary
	$(CC) $(ERL_CFLAGS) -I $(ERL_EI_INCLUDE_DIR) -c -o procket.o procket.c
	$(CC) procket.o -shared -L. $(LDLIBS) -o ../priv/procket.so

clean:
	rm -f *.o *.a ../priv/procket ../priv/procket.so
	$(MAKE) -f Makefile.ancillary clean

.PHONY: all ancillary cmd nif clean