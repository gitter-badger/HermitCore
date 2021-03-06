ARCH = x86
TARGET=x86_64-hermit
MAKE = make
override STRIP_DEBUG = --strip-debug #--strip-unneeded
KEEP_DEBUG = --only-keep-debug

# Set your own cross compiler tool chain prefix here
CROSSCOMPREFIX = x86_64-hermit-

CC_FOR_TARGET = $(CROSSCOMPREFIX)gcc
GO_FOR_TARGET = $(CROSSCOMPREFIX)gccgo
FC_FOR_TARGET = $(CROSSCOMPREFIX)gfortran
CXX_FOR_TARGET = $(CROSSCOMPREFIX)g++
GCC_FOR_TARGET = $(CROSSCOMPREFIX)gcc
CPP_FOR_TARGET = $(CROSSCOMPREFIX)cpp
AR_FOR_TARGET = $(CROSSCOMPREFIX)ar
AS_FOR_TARGET = $(CROSSCOMPREFIX)as
LD_FOR_TARGET = $(CROSSCOMPREFIX)ld
NM_FOR_TARGET = $(CROSSCOMPREFIX)nm
OBJDUMP_FOR_TARGET = $(CROSSCOMPREFIX)objdump
OBJCOPY_FOR_TARGET = $(CROSSCOMPREFIX)objcopy
RANLIB_FOR_TARGET = $(CROSSCOMPREFIX)ranlib
STRIP_FOR_TARGET = $(CROSSCOMPREFIX)strip
READELF_FOR_TARGET = $(CROSSCOMPREFIX)readelf
ELFEDIT_FOR_TARGET = $(CROSSCOMPREFIX)elfedit

# Prettify output
V = 0
ifeq ($V,0)
	Q = @
	P = > /dev/null
endif

# other implicit rules
%.o : %.c
	@echo [CC] $@
	$Q$(CC_FOR_TARGET) -c $(CFLAGS_FOR_TARGET) -o $@ $<

%.o : %.go
	@echo [GO] $@
	$Q$(GO_FOR_TARGET) -c $(GOFLAGS_FOR_TARGET) -o $@ $<

%.o: %.cpp
	@echo [CXX] $@
	$Q$(CXX_FOR_TARGET) -c $(CXXFLAGS_FOR_TARGET) -o $@ $<

%.o: %.f90
	@echo [F90] $@
	$Q$(FC_FOR_TARGET) -c $(FCLAGS_FOR_TARGET) -o $@ $<

default: all

all: hello hello++ thr_hello jacobi hellof RCCE_minimum signals pi server

hello++: hello++.o
	@echo [LD] $@
	$Q$(CXX_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CXXFLAGS_FOR_TARGET)
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

hello: hello.o
	@echo [LD] $@
	$Q$(CC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CFLAGS_FOR_TARGET)
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

hellof: hellof.o
	@echo [LD] $@
	$Q$(FC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(FFLAGS_FOR_TARGET)
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

pi: pi.o
	@echo [LD] $@
	$Q$(GO_FOR_TARGET) -pthread -o $@ $< $(LDFLAGS_FOR_TARGET) $(GOFLAGS_FOR_TARGET)
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

server: server.o
	@echo [LD] $@
	$Q$(GO_FOR_TARGET) -pthread -o $@ $< $(LDFLAGS_FOR_TARGET) $(GOFLAGS_FOR_TARGET) -lnetgo
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

jacobi: jacobi.o
	@echo [LD] $@
	$Q$(CC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CFLAGS_FOR_TARGET)
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

thr_hello.o: thr_hello.c
	@echo [CC] $@
	$Q$(CC_FOR_TARGET) -c $(CFLAGS_FOR_TARGET) -pthread -o $@ $<

thr_hello: thr_hello.o
	@echo [LD] $@
	$Q$(CC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CFLAGS_FOR_TARGET) -pthread
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

signals.o: signals.c
	@echo [CC] $@
	$Q$(CC_FOR_TARGET) -c $(CFLAGS_FOR_TARGET) -pthread -o $@ $<

signals: signals.o
	@echo [LD] $@
	$Q$(CC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CFLAGS_FOR_TARGET) -pthread
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

RCCE_minimum: RCCE_minimum.o
	@echo [LD] $@
	$Q$(CC_FOR_TARGET) -o $@ $< $(LDFLAGS_FOR_TARGET) $(CFLAGS_FOR_TARGET) -lircce
	$Q$(OBJCOPY_FOR_TARGET) $(KEEP_DEBUG) $@ $@.sym
	$Q$(OBJCOPY_FOR_TARGET) $(STRIP_DEBUG) $@
	$Qchmod a-x $@.sym

clean:
	@echo Cleaning tests
	$Q$(RM) hello hello++ hellof jacobi thr_hello RCCE_minimum signals pi server *.sym *.o *~

veryclean:
	@echo Propper cleaning tests
	$Q$(RM) hello hello++ hellof jacobi thr_hello RCCE_minimum pi server *.sym *.o *~

depend:
	$Q$(CC_FOR_TARGET) -MM $(CFLAGS_FOR_TARGET) *.c > Makefile.dep

-include Makefile.dep
# DO NOT DELETE
