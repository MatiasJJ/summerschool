ifeq ($(COMP),)
COMP=pgi
endif

COMMONDIR=../../heat/common

ifeq ($(COMP),cray)
CC=cc
CCFLAGS=-O3 -I/appl/opt/libpng/include -I$(COMMONDIR)
LDFLAGS=-L/appl/opt/libpng/lib
LIBS=-lpng -lz
endif

ifeq ($(COMP),pgi)
CC=mpicc
CCFLAGS=-g -acc -O3 -ta=tesla,cuda10.0,kepler+ -I$(COMMONDIR)
LDFLAGS=-L/homeappl/appl_taito/opt/libpng/lib
LIBS=-lpng
endif

ifeq ($(COMP),gnu)
CC=mpicc
CCFLAGS=-O3 -Wall -I$(COMMONDIR)
LDFLAGS=
LIBS=-lpng
endif

EXE=heat_openacc
OBJS=core.o setup.o utilities.o io.o main.o
OBJS_PNG=$(COMMONDIR)/pngwriter.o

all: vectorsum $(EXE)

vectorsum: vectorsum.c
	$(CC) $(CCFLAGS) $< -o $@

$(COMMONDIR)/pngwriter.o: $(COMMONDIR)/pngwriter.c $(COMMONDIR)/pngwriter.h
core.o: core.c heat.h
utilities.o: utilities.c heat.h
setup.o: setup.c heat.h
io.o: io.c heat.h
main.o: main.c heat.h

$(OBJS_PNG): C_COMPILER := $(CC)
$(OBJS): C_COMPILER := $(CC)

$(EXE): $(OBJS) $(OBJS_PNG)
	$(CC) $(CCFLAGS) $(OBJS) $(OBJS_PNG) -o $@ $(LDFLAGS) $(LIBS)

%.o: %.c
	$(C_COMPILER) $(CCFLAGS) -c $< -o $@

.PHONY: clean
clean:
	-/bin/rm -f $(EXE) vectorsum a.out *.o *.png *~
