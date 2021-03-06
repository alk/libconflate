AC_PREREQ(2.52)
AC_INIT(libconflate, 1.0, dustin@spy.net)
AC_CONFIG_MACRO_DIR([m4])
AC_CANONICAL_SYSTEM
AC_CONFIG_SRCDIR(conflate.h)
AM_INIT_AUTOMAKE(AC_PACKAGE_NAME, AC_PACKAGE_VERSION)
AM_CONFIG_HEADER(config.h)
AC_PROG_LIBTOOL

AC_PROG_CC
AC_PROG_CC_C99

AM_PROG_CC_C_O
AC_PROG_INSTALL

AC_SUBST(PROFILER_LDFLAGS)

AC_ARG_ENABLE(coverage,
  [AS_HELP_STRING([--enable-coverage],[Enable code coverage])])

if test "x$enable_coverage" == "xyes"; then
   if test "$ICC" = "yes"
   then
      :
      dnl ICC trying to be gcc, but not well
   elif test "$GCC" = "yes"
   then
      AC_PATH_PROG([PROFILER], [gcov], "no", [$PATH])
      if test "x$PROFILER" != "xno"; then
         PROFILER_FLAGS="-fprofile-arcs -ftest-coverage"
         PROFILER_LDFLAGS="-lgcov"
      fi
   elif test "$SUNCC" = "yes"
   then
      AC_PATH_PROG([PROFILER], [tcov], "no", [$PATH])
      if test "x$PROFILER" != "xno"; then
         PROFILER_FLAGS=-xprofile=tcov
      fi
   fi
fi
AC_SUBST(PROFILER_FLAGS)

AC_SEARCH_LIBS(pthread_create, pthread,
                               [], AC_MSG_ERROR([Can't enable pthreads.]))
AC_SEARCH_LIBS(sqlite3_open, sqlite3, [],
                             AC_MSG_ERROR([Can't find sqlite3]))
PKG_CHECK_MODULES([CHECK], [check >= 0.9.4])

AC_HEADER_STDBOOL

AC_ARG_WITH([bundled-libstrophe],
        [AS_HELP_STRING([--with-bundled-libstrophe], [force building with bundled libstrophe])])

if test "x$with_bundled_libstrophe" = "xyes"; then
  libstrophe_pkg_missing=yes
else
  PKG_CHECK_MODULES([libstrophe], [libstrophe], [], [
    libstrophe_pkg_missing=yes
  ])
fi
if test "x$libstrophe_pkg_missing" = "xyes"; then
  if test -f "$srcdir/libstrophe/configure"; then
    if test "x$with_bundled_libstrophe" = "xyes"; then
      AC_MSG_NOTICE([forced use of bundled libstrophe])
    else
      AC_MSG_NOTICE([will use bundled libstrophe])
    fi
    AC_CONFIG_SUBDIRS([libstrophe])
    libstrophe_CFLAGS='-I$(top_srcdir)/libstrophe'
    libstrophe_LIBS="-Llibstrophe -lstrophe"
    AC_SUBST([MAYBE_LIBSTROPHE], [libstrophe])
  else
    if test "x$with_bundled_libstrophe" = "xyes"; then
      AC_MSG_FAILURE([use of bundled libstrophe is requested, but libstrophe/configure is missing])
    fi
    AC_MSG_CHECKING([for old version of libstrophe])
    AC_CHECK_HEADER(strophe.h,
            [], AC_MSG_ERROR([Ensure strophe.h is available.]))
    AC_SEARCH_LIBS(xmpp_initialize, strophe,
                                  [], AC_MSG_ERROR([Can't find libstrophe.]))
  fi
fi
AM_CONDITIONAL([BUILD_BUNDLED_LIBSTROPHE], [test "x$build_bundled_libstrophe" = xyes])

AC_C_CONST

dnl Turn up the warnings.
if test "$GCC" = "yes"
then
        CFLAGS="$CFLAGS -Wall -Werror -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls"
fi

AC_CONFIG_FILES(Makefile tests/Makefile libconflate.pc)
AC_OUTPUT
