TAG := $(shell git describe --always --dirty || echo UNKNOWN)

default: libcrucible.so
%.so: Makefile

CRUCIBLE_OBJS = \
	chatter.o \
	cleanup.o \
	crc64.o \
	error.o \
	extentwalker.o \
	fd.o \
	fs.o \
	ntoa.o \
	path.o \
	process.o \
	string.o \
	task.o \
	time.o \
	uuid.o \

include ../makeflags
include ../Defines.mk

configure.h: configure.h.in
	$(TEMPLATE_COMPILER)

.depends/%.dep: %.cc configure.h Makefile
	@mkdir -p .depends
	$(CXX) $(CXXFLAGS) -M -MF $@ -MT $(<:.cc=.o) $<

depends.mk: $(CRUCIBLE_OBJS:%.o=.depends/%.dep)
	cat $^ > $@.new
	mv -f $@.new $@

.version.cc: configure.h Makefile ../makeflags $(CRUCIBLE_OBJS:.o=.cc) ../include/crucible/*.h
	echo "namespace crucible { const char *VERSION = \"$(TAG)\"; }" > $@.new
	mv -f $@.new $@

include depends.mk

%.o: %.cc ../makeflags
	$(CXX) $(CXXFLAGS) -fPIC -o $@ -c $<

libcrucible.so: $(CRUCIBLE_OBJS) .version.o
	$(CXX) $(LDFLAGS) -fPIC -shared -Wl,-soname,$@ -o $@ $^ -luuid
