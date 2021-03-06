override TOPDIR = $(shell pwd)
ARCH = x86
TARGET=x86_64-hermit
NJOBS=-j$(shell nproc)
#OMPRT=libgomp
OMPRT=libomp

CROSSCOMPREFIX = x86_64-hermit
PREFIX = /opt/hermit
RM = rm -rf
CD = cd
CP = cp
MKDIR = mkdir -p
TMP = $(TOPDIR)/tmp
OPT = --disable-shared --disable-multilib --enable-lto --enable-newlib-hw-fp --enable-newlib-io-c99-formats --enable-newlib-multithread #--enable-newlib-reent-small
CLONE_DEPTH = --depth=50
#CLONE_DEPTH =

# Prettify output
V = 0
ifeq ($V,0)
	Q = @
	P = > /dev/null
endif

default:
	@echo Do not use default rule!

examples:
	@echo Build demo applications
	$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET) $(PROFILING_CFLAGS)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET) $(PROFILING_LDFLAGS)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) GO_FOR_TARGET="$(GO_FOR_TARGET)" GOFLAGS_FOR_TARGET="$(GOFLAGS_FOR_TARGET)" -C tests depend
	$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET) $(PROFILING_CFLAGS)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET) $(PROFILING_LDFLAGS)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) GO_FOR_TARGET="$(GO_FOR_TARGET)" GOFLAGS_FOR_TARGET="$(GOFLAGS_FOR_TARGET)" -C tests
	$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET) $(PROFILING_CFLAGS)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET) $(PROFILING_LDFLAGS)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) -C benchmarks depend
	$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET) $(PROFILING_CFLAGS)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET) $(PROFILING_LDFLAGS)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) -C benchmarks
	#$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) -C openmpbench depend
	$Q$(MAKE) ELFEDIT_FOR_TARGET=$(ELFEDIT_FOR_TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET) $(PROFILING_CFLAGS)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET) $(PROFILING_LDFLAGS)" OBJCOPY_FOR_TARGET=$(OBJCOPY_FOR_TARGET) -C openmpbench

bootstrap: binutils gcc newlib pte $(TMP)/binutils $(TMP)/bootstrap

binutils:
	git clone $(CLONE_DEPTH) https://github.com/RWTH-OS/binutils.git

gcc:
	git clone $(CLONE_DEPTH) https://github.com/RWTH-OS/gcc.git

pte:
	git clone $(CLONE_DEPTH) https://github.com/RWTH-OS/pthread-embedded.git pte
	$Q$(CD) pte; ./configure --prefix=$(PREFIX)

newlib:
	git clone $(CLONE_DEPTH) https://github.com/RWTH-OS/newlib.git

$(TMP)/binutils:
	@echo Build binutils
	$Q$(MKDIR) $(TMP)/binutils
	$Q$(CD) $(TMP)/binutils; $(TOPDIR)/binutils/configure --target=$(TARGET) --prefix=$(PREFIX) --with-sysroot --disable-multilib --disable-shared --disable-nls --disable-gdb --disable-libdecnumber --disable-readline --disable-sim --disable-libssp --enable-tls && $(MAKE) $(NJOBS) && $(MAKE) install


$(TMP)/bootstrap:
	@echo Build bootstrap compiler
	$Q$(MKDIR) $(TMP)/bootstrap
	$Q$(CD) $(TMP)/bootstrap; $(TOPDIR)/gcc/configure --target=$(TARGET) --prefix=$(PREFIX) --without-headers --disable-multilib --with-isl --with-tune=generic --enable-languages=c,lto --disable-nls --disable-shared --disable-libssp --disable-libgomp --enable-threads=posix --enable-tls --enable-lto --disable-symvers && $(MAKE) $(NJOBS) all-gcc && $(MAKE) install-gcc

toolchain: $(TMP)/newlib libpthread $(TMP)/gcc libs

$(TMP)/newlib:
	@echo Build newlib
	$Q$(MKDIR) $(TMP)/newlib
	$Q$(CD) $(TMP)/newlib; $(TOPDIR)/newlib/configure --target=$(TARGET) --prefix=$(PREFIX) $(OPT) && $(MAKE) $(NJOBS) && $(MAKE) install

libpthread:
	@echo Build libpthread
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Iplatform/hermit -Iplatform/helper -Wall $(PROFILING_CFLAGS)" -C pte depend
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Iplatform/hermit -Iplatform/helper -Wall $(PROFILING_CFLAGS)" -C pte
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Iplatform/hermit -Iplatform/helper -Wall $(PROFILING_CFLAGS)" -C pte install
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Iplatform/hermit -Iplatform/helper -Wall $(PROFILING_CFLAGS)" -C pte headers

libs:
	@echo Build Xray profiler
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) CFLAGS_FOR_TARGET="$(CFLAGS_FOR_TARGET)" LDFLAGS_FOR_TARGET="$(LDFLAGS_FOR_TARGET)" PREFIX=$(PREFIX) -C xray
	@echo Build OpenMP Runtime and iRCCE
	$Q$(MAKE) TARGET=$(TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Wall -pthread $(PROFILING_CFLAGS)" PREFIX=$(PREFIX) -C $(OMPRT) depend
	$Q$(MAKE) TARGET=$(TARGET) CXX_FOR_TARGET=$(CXX_FOR_TARGET) CXXFLAGS_FOR_TARGET="$(CXXFLAGS_FOR_TARGET)" CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Wall -pthread $(PROFILING_CFLAGS)" PREFIX=$(PREFIX) -C $(OMPRT)
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Wall" PREFIX=$(PREFIX) -C ircce depend
	$Q$(MAKE) TARGET=$(TARGET) CC_FOR_TARGET=$(CC_FOR_TARGET) AR_FOR_TARGET=$(AR_FOR_TARGET) CFLAGS_FOR_TARGET+="-I. -Wall" PREFIX=$(PREFIX) -C ircce

install:
	$Q$(MAKE) PREFIX=$(PREFIX) -C xray install
	$Q$(MAKE) PREFIX=$(PREFIX) -C ircce install
	$Q$(MAKE) PREFIX=$(PREFIX) -C $(OMPRT) install

$(TMP)/gcc:
	@echo Build final gcc
	$Q$(MKDIR) $(TMP)/gcc
	$Q$(CD) $(TMP)/gcc; $(TOPDIR)/gcc/configure --target=$(TARGET) --prefix=$(PREFIX) --with-newlib --with-isl --disable-multilib --without-libatomic --with-tune=generic --enable-languages=c,c++,go,fortran,lto --disable-nls --disable-shared --disable-libssp --enable-threads=posix --disable-libgomp --enable-tls --enable-lto --disable-symvers && $(MAKE) $(NJOBS) all-gcc; $(MAKE) install-all-gcc; $(MAKE) $(NJOBS) all-target-libgcc && $(MAKE) install-target-libgcc && $(MAKE) $(NJOBS) &&  $(MAKE) install

clean:
	@echo Cleaning toolchain
	$Q$(MAKE) -C $(OMPRT) clean
	$Q$(MAKE) -C ircce clean
	$Q$(MAKE) -C tests clean
	$Q$(MAKE) -C benchmarks clean
	$Q$(MAKE) -C openmpbench clean
	$Q$(MAKE) -C xray clean

veryclean:
	@echo Propper cleaning of the toolchain
	$Q$(MAKE) -C $(OMPRT) veryclean
	$Q$(MAKE) -C ircce veryclean
	$Q$(MAKE) -C tests veryclean
	$Q$(MAKE) -C benchmarks veryclean
	$Q$(MAKE) -C openmpbench veryclean
	$Q$(RM) binutils gcc newlib pte
	$Q$(RM) $(TMP)

.PHONY: default all clean veryclean headers libpthread libs
