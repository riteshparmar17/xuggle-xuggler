#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

# IMPORTANT: IF YOU CHANGE THESE VERSION NUMBERS YOU MUST
# ALSO CHANGE ANY CORRESPONDING VERSION NUMBERS IN THE
# JAVA build.xml FILE
m4_define([VSM4_LIB_MAJOR_VERSION], [5])
m4_define([VSM4_LIB_MINOR_VERSION], [5])

AC_INIT([xuggler],[VSM4_LIB_MAJOR_VERSION.VSM4_LIB_MINOR_VERSION],[info@xuggle.com])
AC_PREREQ([2.68])
AC_PREFIX_DEFAULT([${XUGGLE_HOME:-/usr/local}])
AM_INIT_AUTOMAKE([-Wall -Werror no-dependencies nostdinc tar-pax 1.11.1 ])
AM_MAINTAINER_MODE
AM_SILENT_RULES([yes])
LT_INIT([disable-static disable-fast-install win32-dll])

AC_CANONICAL_BUILD
AC_CANONICAL_HOST

# I hate building on Mac even more than windows; really; it's hard to explain my loathing
CFLAGS="${CFLAGS}"
CXXFLAGS="${CXXFLAGS}"
LDFLAGS="${LDFLAGS}"
ASFLAGS="${ASFLAGS}"
case $host_os in
  *darwin*)
  case $host_cpu in
     *x86_64*|*amd64*)
     CFLAGS="-arch x86_64 ${CFLAGS}"
     CXXFLAGS="-arch x86_64 ${CXXFLAGS}"
     LDFLAGS="-arch x86_64 ${LDFLAGS}"
     ;;
     *i?86*)
     CFLAGS="-arch i386 ${CFLAGS}"
     CXXFLAGS="-arch i386 ${CXXFLAGS}"
     LDFLAGS="-arch i386 ${LDFLAGS}"
     ;;
     *ppc64*|*powerpc64*)
     CFLAGS="-arch ppc64 ${CFLAGS}"
     CXXFLAGS="-arch ppc64 ${CXXFLAGS}"
     LDFLAGS="-arch ppc64 ${LDFLAGS}"
     ;;
     *ppc*|*powerpc*)
     CFLAGS="-arch ppc ${CFLAGS}"
     CXXFLAGS="-arch ppc ${CXXFLAGS}"
     LDFLAGS="-arch ppc ${LDFLAGS}"
     ;;
  esac
  ;;
esac
AC_SUBST([ASFLAGS])
# Checks for programs.
AC_PROG_CC
AC_PROG_CXX
AC_LANG(C++)

# This will be set to true if we are cross-compiling and will be accessible
# in Makefile.am
AC_MSG_CHECKING([for cross-compiling])
if test "x$host_alias" != "x"; then
  VS_CROSS_COMPILE=1
  AC_MSG_RESULT([yes])
  AC_MSG_CHECKING([for separate cross-compiling toolchain])
  if $( echo "${CC}" | grep -q "^${host_alias}-" ) ; then
    VS_CROSS_PREFIX="${host_alias}-"
    AC_MSG_RESULT([yes: \"${VS_CROSS_PREFIX}\" ]) #"
  else
    VS_CROSS_PREFIX=""
    AC_MSG_RESULT([no])
  fi
else
  AC_MSG_RESULT([no])
fi 
AC_SUBST([VS_CROSS_PREFIX])
AC_SUBST([VS_CROSS_COMPILE])
AM_CONDITIONAL([VS_CROSS_COMPILE], [ test "x$host_alias" != "x" ])

LIB_MAJOR_VERSION=VSM4_LIB_MAJOR_VERSION
LIB_MINOR_VERSION=VSM4_LIB_MINOR_VERSION
AC_SUBST([LIB_MAJOR_VERSION])
AC_SUBST([LIB_MINOR_VERSION])
AC_DEFINE(VS_LIB_MAJOR_VERSION, [VSM4_LIB_MAJOR_VERSION], [Major version of library])
AC_DEFINE(VS_LIB_MINOR_VERSION, [VSM4_LIB_MINOR_VERSION], [Minor version of library])

#
# CAPTIVE building support
#
AC_ARG_ENABLE([captives],
              [AS_HELP_STRING([--enable-captives],
                              [use our own captive versions of dependent libraries (default is yes)])],
                              [],
                              [enable_captives=yes])
AC_MSG_CHECKING([whether to use captive versions of dependent libraries])
AC_MSG_RESULT([$enable_captives])
CAPTIVES_ENABLED=$enable_captives
AM_CONDITIONAL(CAPTIVES_ENABLED,
               [ test "x$CAPTIVES_ENABLED" = "xyes" ])
AC_SUBST([CAPTIVES_ENABLED])

AC_CONFIG_MACRO_DIR([m4])

m4_include([m4/ax_compiler_vendor.m4])
m4_include([m4/xuggle.m4])

AX_COMPILER_VENDOR
AM_CONDITIONAL([VS_IS_GCC], [ test "$ax_cv_c_compiler_vendor" = gnu ])

if test "x$enable_captives" = "xyes"; then
  AC_CONFIG_SUBDIRS([captive])
fi

VS_CPPFLAGS=
VS_CFLAGS=
VS_CXXFLAGS=
VS_LDFLAGS=

AC_ARG_ENABLE([optimizations],
              [AS_HELP_STRING([--enable-optimizations],
                              [build libraries and executables with compiler optimizations (default is yes)])],
                              [],
                              [enable_optimizations=yes])
AC_MSG_CHECKING([whether to enable compiler optimizations])
AC_MSG_RESULT([$enable_optimizations])

AM_CONDITIONAL([VS_DEBUG], [ test "$x$enable_optimizations" = xno ])
if test "x$enable_optimizations" = xno; then
  VS_DEBUG=1
  AC_SUBST([VS_DEBUG])
fi

AC_ARG_ENABLE([swig],
              [AS_HELP_STRING([--enable-swig],
                              [use swig to regenerate java interfaces (default is no)])],
                              [],
                              [enable_swig=no])
AC_MSG_CHECKING([whether to regnerate java interfaces from swig])
AC_MSG_RESULT([$enable_swig])

AM_CONDITIONAL([VS_SWIG], [ test "$x$enable_swig" = xno ])
if test "x$enable_swig" != xno; then
  AC_PATH_PROG([SWIG],[swig])
  AC_ARG_VAR(SWIG,
             [Location of the 'swig' program; if found, then we will renerate the Java interface to Xuggle from the C++ interface])
  if test "x$SWIG" = "x"; then
    AC_MSG_FAILURE([Could not find swig, and it is required to regenerate java interfaces])
  else
    VS_SWIG=1
    AC_SUBST([VS_SWIG])
  fi
fi

AC_MSG_CHECKING([whether we saw a bunny])
AC_MSG_RESULT([no])

HOST_OS=$host
case $host in
  *mingw*|*cygwin*)
  HOST_TYPE=Windows
  ;;
  *darwin*)
  HOST_TYPE=Mac
  ;;
  *linux*)
  HOST_TYPE=Linux
  VS_LDFLAGS+="-Wl,-Bsymbolic"
  ;;
  *)
  HOST_TYPE=Linux
  ;;
esac
enable_warningerrors=yes
case $HOST_TYPE in
  Windows)
  HOST_SO_LIBPATH=[PATH]
  VS_OS_WINDOWS=1
  AC_SUBST([VS_OS_WINDOWS])
  dnl For libtool
  export need_lib_prefix=no
  dnl Turns out older versions of G++ on windows have warnings
  dnl in their std:: header files.  shame on them.
  enable_warningerrors=no
  ;;
  Mac)
  HOST_SO_LIBPATH=[DYLD_LIBRARY_PATH]
  ;;
  Linux)
  HOST_SO_LIBPATH=[LD_LIBRARY_PATH]
  ;;
esac
AM_CONDITIONAL([VS_OS_WINDOWS], [ test "${HOST_TYPE}" = "Windows" ])
AM_CONDITIONAL([VS_OS_LINUX], [ test "${HOST_TYPE}" = "Linux" ])
AM_CONDITIONAL([VS_OS_MAC], [ test "${HOST_TYPE}" = "Mac" ])

AC_SUBST([HOST_TYPE])
AC_SUBST([HOST_OS])
AC_SUBST([HOST_SO_LIBPATH])

AC_MSG_CHECKING([whether we smell bunny poop])
AC_MSG_RESULT([no])

LT_PREREQ([2.2.6])
dnl AC_LIBTOOL_WIN32_DLL
dnl AC_PROG_LIBTOOL
dnl AM_PROG_LIBTOOL
AC_SUBST([LIBTOOL_DEPS])


AC_FUNC_STRERROR_R
AC_MSG_CHECKING([for bunny tracks])
AC_MSG_RESULT([no])
AC_PATH_PROG([VALGRIND],[valgrind])
AC_ARG_VAR(VALGRIND,
          [Location of the 'valgrind' program; if found, then "ant run-tests-memcheck" will do full memory and error checking])
AM_CONDITIONAL(HAVE_VALGRIND, [ test "x$VALGRIND" != "x" ])

AC_MSG_CHECKING([for bunny fur])
AC_MSG_RESULT([no])

AC_PATH_PROG([DOXYGEN],[doxygen])
AC_ARG_VAR(DOXYGEN,
           [Location of the 'doxygen' program; if found, then we will renerate documentation from the C++ interface])
AM_CONDITIONAL(HAVE_DOXYGEN, [ test "x$DOXYGEN" != "x" ])

AC_PATH_PROG([PERL],[perl])
AC_ARG_VAR(PERL,
          [Location of the 'perl' program; [PERL] is required if you add new tests or change exisitng tests.])
AM_CONDITIONAL(HAVE_PERL, [ test "x$PERL" != "x" ])

#----------------------------------------------------------------
# Look for java
#----------------------------------------------------------------

AC_MSG_CHECKING(for java include file jni.h)
AC_ARG_WITH(javaincl, [  --with-javaincl=path    Set location of Java include directory], [JAVAINCDIR="$withval"], [JAVAINCDIR=])

if test -z "$JAVA_HOME" ; then
  JAVA_HOME=":" # Something completely invalid
fi
if test -z "$JAVAINCDIR" ; then
  # Cross compiling; use the captive JNI
  # And annoyingly at this stage the relative srcdir autoconf variable is
  # set, but not the absolute path.  so we fake it.
  VS_ABS_PATH=$( cd "${srcdir}" && pwd )
  case $host in
    *cygwin*|*mingw*)
      JAVAINCDIR="${VS_ABS_PATH}/captive/jni/mingw32/"
    ;;
    *darwin*)
      JAVAINCDIR="${VS_ABS_PATH}/captive/jni/darwin/"
    ;;
    *linux*)
      JAVAINCDIR="${VS_ABS_PATH}/captive/jni/linux/"
    ;;
    *)
    # And here we add several common locations on Unix like systems to try
    # to get them to build
    JAVAINCDIR="${JAVAINCDIR} ${JAVA_HOME}/include /usr/j2sdk*/include /usr/local/j2sdk*/include /usr/jdk*/include /usr/local/jdk*/include /opt/j2sdk*/include /opt/jdk*/include /usr/java/include /usr/java/j2sdk*/include /usr/java/jdk*/include /usr/local/java/include /opt/java/include /usr/include/java /usr/local/include/java /usr/lib/java/include /usr/lib/jvm/java*/include /usr/include/kaffe /usr/local/include/kaffe /usr/include"
     ;;
  esac
fi

VS_JNI_INCLUDE=""
for d in $JAVAINCDIR ; do
  if test -r "$d/jni.h" ; then
    AC_MSG_RESULT($d)
    JAVAINCDIR=$d
    VS_JNI_INCLUDE=-I\"$d\" #" quote here to fix nasty formatting bug in VIM
    break
  fi
done

if test "$VS_JNI_INCLUDE" = "" ; then
  AC_MSG_RESULT(not found)
  AC_MSG_FAILURE([Could not find required Java header file jni.h. 
                  Try defining JAVA_HOME to point to where you've #'
                  installed the JAVA JDK])
else
  # now look for <arch>/jni_md.h
  AC_MSG_CHECKING(for java include file jni_md.h)
  JAVAMDDIR=`find "$JAVAINCDIR" -follow -name jni_md.h -print`
  if test "$JAVAMDDIR" = "" ; then
    AC_MSG_RESULT(not found)
    AC_MSG_FAILURE([Could not find required Java header file jni_md.h. 
                  Try defining JAVA_HOME to point to where you've 
                  installed the JAVA JDK]) #'
  else
    JAVAMDDIR=`dirname "$JAVAMDDIR" | tail -1`
    VS_JNI_INCLUDE="${VS_JNI_INCLUDE} -I\"$JAVAMDDIR\""
    AC_MSG_RESULT($JAVAMDDIR)
  fi
fi

AC_SUBST([VS_JNI_INCLUDE])


# Checks for header files.
AC_HEADER_STDC
AC_MSG_CHECKING([for carrot remnants])
AC_MSG_RESULT([no])
AC_HEADER_ASSERT
AC_CHECK_HEADERS([stdlib.h string.h unistd.h])
# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_C_CONST
AC_MSG_CHECKING([for wabbit season])
AC_MSG_RESULT([duck season])
AC_C_INLINE
AC_TYPE_INT64_T
AC_TYPE_INT32_T
AC_TYPE_INT16_T
AC_TYPE_INT8_T
AC_TYPE_UINT64_T
AC_TYPE_UINT32_T
AC_TYPE_UINT16_T
AC_TYPE_UINT8_T
AC_TYPE_SIZE_T
AC_TYPE_OFF_T
AC_STRUCT_TM
AC_MSG_CHECKING([if i'm gonna get you wabbit]) #'
AC_MSG_RESULT([no])
AC_C_VOLATILE

# Checks for library functions.
AC_FUNC_ERROR_AT_LINE
AC_FUNC_MALLOC
# Check for 64-bit stdio; not available on windows
AC_FUNC_FSEEKO
AC_CHECK_FUNCS([memmove memset])

AC_LANG_WERROR
AC_MSG_CHECKING([if you're now hunting rabbits as well]) #'
AC_MSG_RESULT([yes])

AC_PROG_CPP_WERROR

AC_ARG_ENABLE([warningerrors],
              [AS_HELP_STRING([--enable-warningerrors],
                              [treat warnings as errors that stop the build (default is $enable_warningerrors)])],
                              [],
                              [])
AC_MSG_CHECKING([whether to treat warnings as errors])
AC_MSG_RESULT([$enable_warningerrors])
AM_CONDITIONAL([VS_WARN_ISERROR], [ test "x$enable_warningerrors" = xyes ])
if test "x$enable_warningerrors" = xyes; then
  VS_WARN_ISERROR=1
  AC_SUBST([VS_WARN_ISERROR])
fi

AC_DEFINE(VSJNI_USE_JVM_FOR_MEMMANAGEMENT, 1, AC_PACKAGE_NAME [ will use Java to allocate large memory blobs if running inside a Java Virtual Machine])

AC_MSG_CHECKING([if you smiled])
AC_MSG_RESULT([let us know: info@xuggle.com])

enable_gpl=yes
VS_ENABLE_GPL=$enable_gpl
AC_DEFINE(VS_ENABLE_GPL, 1, AC_PACKAGE_NAME [ will enable any code that depends on GPL code ])
AC_SUBST(VS_ENABLE_GPL)
AM_CONDITIONAL(VS_ENABLE_GPL,
               [ test "x$VS_ENABLE_GPL" = "xyes" ])

dnl substitute our values for compiling; we don't reset the
dnl CFLAGS values because then they'll be propagated to captives
AC_SUBST([VS_CFLAGS])dnl
AC_SUBST([VS_CXXFLAGS])dnl
AC_SUBST([VS_CPPFLAGS])dnl
AC_SUBST([VS_LDFLAGS])dnl

VS_FIND_STATIC_LIB([stdc++],
[VS_LIBSTDCXX="-XCClinker ${VS_LIB}"], [VS_LIBSTDCXX="-XCClinker -lstdc++"])
VS_FIND_STATIC_LIB([gcc],
[VS_LIBGCC="-XCClinker ${VS_LIB}"], [])

case $host in
  *mingw*|*cygwin*)
  # on windows use the shared library; it'll be there. trust me.
  VS_LIBSTDCXX="-XCClinker -lstdc++"
  ;;
esac

AC_SUBST([VS_LIBSTDCXX])
AC_SUBST([VS_LIBGCC])

dnl Now, output everything
AC_CONFIG_SRCDIR([csrc/com/xuggle/Xuggle.h])
AC_CONFIG_HEADER([csrc/com/xuggle/ferry/config.h:config.h.in])
AC_CONFIG_HEADER([csrc/com/xuggle/xuggler/config.h:config.h.in])
AC_CONFIG_HEADER([csrc/com/xuggle/xuggler/io/config.h:config.h.in])
AC_CONFIG_FILES([Makefile
                 mk/Makefile.global:mk/buildtools/Makefile.global.in
                 csrc/Makefile
                 csrc/com/Makefile
                 csrc/com/xuggle/Makefile
                 csrc/com/xuggle/ferry/Makefile
                 csrc/com/xuggle/xuggler/Makefile
                 csrc/com/xuggle/Makefile.dependencies:csrc/com/xuggle/Makefile.dependencies.in
                 csrc/com/xuggle/xuggler/io/Makefile
                 test/csrc/Makefile
                 test/csrc/com/Makefile
                 test/csrc/com/xuggle/Makefile
                 test/csrc/com/xuggle/testutils/Makefile
                 test/csrc/com/xuggle/ferry/Makefile
                 test/csrc/com/xuggle/xuggler/Makefile
                 test/csrc/com/xuggle/xuggler/io/Makefile
                 ])
AC_OUTPUT
