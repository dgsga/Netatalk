# Makefile.am for bin/misc

pkgconfdir = @PKGCONFDIR@
bin_PROGRAMS =

noinst_PROGRAMS = netacnv logger_test fce

netacnv_SOURCES = netacnv.c
netacnv_LDADD = $(top_builddir)/libatalk/libatalk.la

logger_test_SOURCES = logger_test.c
logger_test_LDADD = $(top_builddir)/libatalk/libatalk.la

fce_SOOURCE = fce.c
fce_LDADD = $(top_builddir)/libatalk/libatalk.la
fce_CFLAGS = -I$(top_srcdir)/include

