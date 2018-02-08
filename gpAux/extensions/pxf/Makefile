MODULE_big = pxf
OBJS       = src/pxfprotocol.o src/pxfbridge.o src/pxfuriparser.o src/libchurl.o src/pxfutils.o src/pxfheaders.o src/pxffragment.o src/gpdbwritableformatter.o
REGRESS    = setup pxf pxfinvalid

ifdef USE_PGXS
	PGXS := $(shell pg_config --pgxs)
include $(PGXS)
else
	top_builddir = ../../..
	include $(top_builddir)/src/Makefile.global
	include $(top_srcdir)/contrib/contrib-global.mk
endif

SHLIB_LINK += -lcurl

install: pxf.so
	sed "s/{{revision}}/$(shell git log -1 --pretty=format:"%H" .)/g" pxf--1.0.sql > pxf.sql
	$(INSTALL_PROGRAM) pxf.sql $(DESTDIR)$(datadir)/cdb_init.d/pxf.sql && rm -f pxf.sql

uninstall:
	rm -f '$(DESTDIR)$(datadir)/cdb_init.d/pxf.sql'

unittest-check:
	$(MAKE) -C test check

unittest-clean:
	$(MAKE) -C test clean

clean: unittest-clean

.PHONY: unittest-check unittest-clean
