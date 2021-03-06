dnl configure.in for netatalk

AC_INIT([netatalk], [m4_esyscmd([cat VERSION])])
AC_CANONICAL_SYSTEM
AM_INIT_AUTOMAKE([subdir-objects])
AM_CONFIG_HEADER(config.h)
AC_CONFIG_MACRO_DIRS([macros])
AM_MAINTAINER_MODE([enable])
AM_SILENT_RULES([yes])

dnl Checks for programs.
AC_PROG_AWK
AC_PROG_CC
AC_PROG_CC_C99
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
AC_LIBTOOL_DLOPEN
LT_INIT
RANLIB='ranlib -no_warning_for_no_symbols'
AC_PROG_GREP
AC_PROG_PS

AM_PROG_CC_C_O

dnl Checks for header files.
AC_HEADER_DIRENT
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS(fcntl.h limits.h stdint.h strings.h time.h sys/param.h sys/fcntl.h sys/file.h sys/ioctl.h sys/time.h sys/mnttab.h sys/statvfs.h sys/stat.h sys/vfs.h mntent.h syslog.h unistd.h termios.h sys/termios.h netdb.h sgtty.h ufs/quota.h mount.h statfs.h sys/types.h dlfcn.h errno.h sys/errno.h sys/uio.h langinfo.h locale.h sys/filio.h)
AC_CHECK_HEADERS([sys/mount.h], , , 
[#ifdef HAVE_SYS_PARAM_H
#include <sys/param.h>
#endif
])

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_UID_T
AC_C_INLINE
AC_TYPE_MODE_T
AC_TYPE_OFF_T
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_STRUCT_ST_RDEV
AC_HEADER_TIME
AC_STRUCT_TM

dnl --------------------------------------------------------------------------
dnl check if dlsym needs to add an underscore, uses libtool macros 
dnl --------------------------------------------------------------------------
AC_LTDL_DLLIB
AC_CHECK_FUNCS(dlopen dlsym dlclose)
AC_LTDL_DLSYM_USCORE
if test x"$libltdl_cv_need_uscore" = xyes; then
    AC_DEFINE(DLSYM_PREPEND_UNDERSCORE, 1, [BSD compatibility macro])
fi

dnl Checks for library functions.
AC_TYPE_GETGROUPS
AC_PROG_GCC_TRADITIONAL
AC_FUNC_MEMCMP
AC_HEADER_MAJOR
AC_FUNC_MMAP
AC_TYPE_SIGNAL
AC_FUNC_UTIME_NULL
AC_FUNC_WAIT3
AC_CHECK_FUNCS(getcwd gethostname gettimeofday getusershell mkdir rmdir select socket strdup strcasestr strstr strtoul strchr memcpy)
AC_CHECK_FUNCS(backtrace_symbols setlocale nl_langinfo strlcpy strlcat setlinebuf dirfd pselect access pread pwrite)
AC_CHECK_FUNCS(waitpid getcwd strdup strndup strnlen strtoul strerror chown fchown chmod fchmod chroot link mknod mknod64)
ac_neta_haveatfuncs=yes
AC_CHECK_FUNCS(openat renameat fstatat unlinkat, , ac_neta_haveatfuncs=no)
if test x"$ac_neta_haveatfuncs" = x"yes" ; then
   AC_DEFINE([_ATFILE_SOURCE], 1, AT file source)
   AC_DEFINE([HAVE_ATFUNCS], 1, whether at funcs are available)
fi

AC_CHECK_MEMBERS(struct tm.tm_gmtoff,,, [#include <time.h>])

AC_CHECK_FUNC(gethostbyname,,[AC_CHECK_LIB(nsl,gethostbyname)])
AC_CHECK_FUNC(connect,,[AC_CHECK_LIB(socket,connect)])
dnl search for necessary libs for libpthread stuff
AC_SEARCH_LIBS(pthread_sigmask, pthread,, 
               [AC_MSG_ERROR([cannot find pthread_sigmask in libc or libpthread])])
if test x"$ac_cv_search_pthread_sigmask" != x"none required" ; then
   PTHREAD_LIBS=$ac_cv_search_pthread_sigmask
fi
AC_SUBST(PTHREAD_LIBS)
AC_CACHE_SAVE

dnl Checks for (v)snprintf
NETATALK_SNPRINTF_CHECK

dnl --------------------------------------------------------------------------
dnl 64bit platform check
dnl --------------------------------------------------------------------------

AC_MSG_CHECKING([whether to check for 64bit libraries])
# Test if the compiler is in 64bit mode
echo 'int i;' > conftest.$ac_ext
atalk_cv_cc_64bit_output=no
if AC_TRY_EVAL(ac_compile); then
    case `/usr/bin/file conftest.$ac_objext` in
    *"ELF 64"*)
      atalk_cv_cc_64bit_output=yes
      ;;
    esac
fi
rm -rf conftest*

case $host_cpu:$atalk_cv_cc_64bit_output in
powerpc64:yes | x86_64:yes | i386:yes)
    case $target_os in
    *)
        AC_MSG_RESULT([yes])
        atalk_libname="lib64"
        ;;
    esac
    ;;
*:*)
    AC_MSG_RESULT([no])
    atalk_libname="lib"
    ;;
esac

dnl --------------------------------------------------------------------------
dnl specific configuration comes in here:
dnl --------------------------------------------------------------------------


netatalk_cv_admin_group=yes
AC_MSG_CHECKING([for administrative group support])
AC_ARG_ENABLE(admin-group,
 	[  --disable-admin-group   disable admin group],[
        if test x"$enableval" = x"no"; then
		AC_DEFINE(ADMIN_GRP, 0, [Define if the admin group should be enabled])
		netatalk_cv_admin_group=no
		AC_MSG_RESULT([no])
	else
		AC_DEFINE(ADMIN_GRP, 1, [Define if the admin group should be enabled])
		AC_MSG_RESULT([yes])
        fi],[
		AC_DEFINE(ADMIN_GRP, 1, [Define if the admin group should be enabled])
		AC_MSG_RESULT([yes])
	]
)

NETATALK_AFS_CHECK

NETATALK_CONFIG_DIRS

AC_MSG_CHECKING([whether to enable debug code])
AC_ARG_ENABLE(debug1,
	[  --enable-debug1         enable debug code],[
	if test "$enableval" != "no"; then
		if test "$enableval" = "yes"; then
			AC_DEFINE(DEBUG1, 1, [Define if debugging information should be included])
		else
			AC_DEFINE_UNQUOTED(DEBUG1, $enableval, [Define if debugging information should be included])
		fi 
		AC_MSG_RESULT([yes])
	else
		AC_MSG_RESULT([no])
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

AC_MSG_CHECKING([whether to enable verbose debug code])
AC_ARG_ENABLE(debug,
	[  --enable-debug          enable verbose debug code],[
	if test "$enableval" != "no"; then
		if test "$enableval" = "yes"; then
			AC_DEFINE(DEBUG, 1, [Define if verbose debugging information should be included])
		else
			AC_DEFINE_UNQUOTED(DEBUG, $enableval, [Define if verbose debugging information should be included])
		fi 
		AC_MSG_RESULT([yes])
	else
		AC_MSG_RESULT([no])
        AC_DEFINE(NDEBUG, 1, [Disable assertions])
	fi
	],[
		AC_MSG_RESULT([no])
        AC_DEFINE(NDEBUG, 1, [Disable assertions])
	]
)

AC_MSG_CHECKING([whether to enable debugging with debuggers])
AC_ARG_ENABLE(debugging,
	[  --enable-debugging      disable SIGALRM timers and DSI tickles (eg for debugging with gdb/dbx/...)],[
	if test "$enableval" != "no"; then
		if test "$enableval" = "yes"; then
			AC_DEFINE(DEBUGGING, 1, [Define if you want to disable SIGALRM timers and DSI tickles])
		else
			AC_DEFINE_UNQUOTED(DEBUGGING, $enableval, [Define if you want to disable SIGALRM timers and DSI tickles])
		fi 
		AC_MSG_RESULT([yes])
	else
		AC_MSG_RESULT([no])
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

AC_SYS_LARGEFILE([], AC_MSG_ERROR([AFP 3.x support requires Large File Support.]))
AC_CHECK_ICONV

netatalk_cv_force_uidgid=no
AC_MSG_CHECKING([whether to enable forcing of uid/gid per volume])
AC_ARG_ENABLE(force-uidgid,
	[  --enable-force-uidgid   allow forcing of uid/gid per volume (BROKEN) ],[
	if test "$enableval" = "yes"; then
		AC_DEFINE(FORCE_UIDGID, 1, [Define if you want forcing of uid/gid per volume])
		AC_MSG_RESULT([enabling forcing of uid/gid per volume])
		AC_MSG_RESULT([yes])
		netatalk_cv_force_uidgid=yes
	else
		AC_MSG_RESULT([no])
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

dnl Check for CNID database backends
bdb_required=no
AC_NETATALK_CNID([bdb_required=yes],[bdb_required=no])

dnl Check for optional server location protocol support (used by MacOS X)
NETATALK_SRVLOC

dnl Check for optional Zeroconf support
NETATALK_ZEROCONF

dnl Check for PAM libs
netatalk_cv_use_pam=no
AC_PATH_PAM([
	use_pam_so=yes
	compile_pam=yes
	netatalk_cv_use_pam=yes
	AC_DEFINE(USE_PAM, 1, [Define to enable PAM support])
])

netatalk_cv_use_shadowpw=no
AC_ARG_WITH(shadow,
	[  --with-shadow           enable shadow password support [[auto]]],
	[netatalk_cv_use_shadowpw="$withval"],
	[netatalk_cv_use_shadowpw=auto]
)

if test "x$netatalk_cv_use_shadowpw" != "xno"; then
    AC_CHECK_HEADER([shadow.h])
    if test x"$ac_cv_header_shadow_h" = x"yes"; then
	netatalk_cv_use_shadowpw=yes
	AC_DEFINE(SHADOWPW, 1, [Define if shadow passwords should be used])
    else 
      if test "x$shadowpw" = "xyes"; then
        AC_MSG_ERROR([shadow support not available])
      else
       	netatalk_cv_use_shadowpw=no
      fi
    fi 
fi

AC_MSG_CHECKING([whether shadow support should be enabled])
if test "x$netatalk_cv_use_shadowpw" = "xyes"; then
	AC_MSG_RESULT([yes])
else
	AC_MSG_RESULT([no])
fi
	
	
	
netatalk_cv_use_shellcheck=yes
AC_MSG_CHECKING([whether checking for a valid shell should be enabled])
AC_ARG_ENABLE(shell-check,
	[  --disable-shell-check   disable checking for a valid shell],[
	if test "$enableval" = "no"; then 
		AC_DEFINE(DISABLE_SHELLCHECK, 1, [Define if shell check should be disabled])
		AC_MSG_RESULT([no])
		netatalk_cv_use_shellcheck=no
	else
		AC_MSG_RESULT([yes])
	fi
	],[
		AC_MSG_RESULT([yes])
	]
)

NETATALK_TCP_WRAPPERS

AC_MSG_CHECKING([whether system (fcntl) locking should be disabled])
AC_ARG_ENABLE(locking,
        [  --disable-locking       disable system locking],[
        	if test "$enableval" = "no"; then
                	AC_DEFINE(DISABLE_LOCKING, 1, [Define if system (fcntl) locking should be disabled])
	                AC_MSG_RESULT([yes])
		else
	                AC_MSG_RESULT([no])
        	fi
		
	],[
		AC_MSG_RESULT([no])
	]

)

AC_ARG_WITH(uams-path,
	[  --with-uams-path=PATH   path to UAMs [[PKGCONF/uams]]],[
		uams_path="$withval"
	],[
		uams_path="${PKGCONFDIR}/uams"
	]
)

NETATALK_AC_CUPS

dnl check if we can use attribute unused (gcc only) from ethereal
AC_MSG_CHECKING(to see if we can add '__attribute__((unused))' to CFLAGS)
if test x$GCC != x ; then
  CFLAGS="-D_U_=\"__attribute__((unused))\" $CFLAGS"
  AC_MSG_RESULT(yes)
else
  CFLAGS="-D_U_=\"\" $CFLAGS"
  AC_MSG_RESULT(no)
fi

dnl --------------------------------------------------------------------------
dnl FHS stuff has to be done last because it overrides other defaults
dnl --------------------------------------------------------------------------

AC_MSG_CHECKING([whether to use Filesystem Hierarchy Standard (FHS) compatibility])
AC_ARG_ENABLE(fhs,
	[  --enable-fhs            use Filesystem Hierarchy Standard (FHS) compatibility],[
	if test "$enableval" = "yes"; then
		uams_path="${libdir}/netatalk"
		sysconfdir="/etc"
		PKGCONFDIR=${sysconfdir}/netatalk
		SERVERTEXT="${PKGCONFDIR}/msg"
		use_pam_so=yes
		mandir="/usr/share/man"
		AC_DEFINE(FHS_COMPATIBILITY, 1, [Define if you want compatibily with the FHS])
		AC_MSG_RESULT([yes])
	else
		AC_MSG_RESULT([no])
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

dnl --------------------------------------------------------------------------
dnl post-FHS substitutions, etc
dnl --------------------------------------------------------------------------

dnl ***** UAMS_PATH
dnl AC_DEFINE_UNQUOTED(UAMS_PATH, "${uams_path}",
dnl 	[path to UAMs [default=PKGCONF/uams]])
UAMS_PATH="${uams_path}"
AC_SUBST(UAMS_PATH)

dnl --------------------------------------------------------------------------
dnl drop in includes for top level directory structures here...
dnl --------------------------------------------------------------------------

dnl Note: $(top_srcdir)/include should be added before all other includes
dnl       so that includes from that directory a preferred to includes from
dnl       /usr/include or similar places.
LIBS="$LIBS -L\$(top_srcdir)/libatalk"
CFLAGS="-I\$(top_srcdir)/include $CFLAGS -I\$(top_srcdir)/sys"

AC_DEFINE(OPEN_NOFOLLOW_ERRNO, ELOOP, errno returned by open with O_NOFOLLOW)

dnl --------------------------------------------------------------------------
dnl specific configuration comes in here:
dnl --------------------------------------------------------------------------

dnl ac_cv_target_os is now host_os, ac_cv_target_cpu is now host_cpu

dnl --------------------- determine operating system from "target"
case "$host_os" in
	*osx*)				this_os=macosx ;;
	*darwin*)			this_os=macosx ;;
esac

case "$host_cpu" in
	i386|i486|i586|i686|k7)		this_cpu=x86 ;;
	powerpc|ppc)				this_cpu=ppc ;;
esac

dnl --------------------- operating system specific flags (port from sys/*)

dnl ----- Mac OSX specific -----
if test x"$this_os" = "xmacosx"; then 
	AC_MSG_RESULT([ * Mac OSX specific configuration])
	AC_DEFINE(BSD4_4, 1, [BSD compatiblity macro])
	AC_DEFINE(HAVE_2ARG_DBTOB, 1, [Define if dbtob takes two arguments])
	AC_DEFINE(MACOSX_SERVER, 1, [Define if compiling for MacOS X Server])
fi

dnl ------ Check for sendfile() --------
netatalk_cv_search_sendfile=yes
AC_ARG_ENABLE(sendfile,
    [  --disable-sendfile       disable sendfile syscall],
    [if test x"$enableval" = x"no"; then
            netatalk_cv_search_sendfile=no
        fi]
)

if test x"$netatalk_cv_search_sendfile" = x"yes"; then
   case "$host_os" in
   *)
        ;;

    esac

    if test x"$netatalk_cv_HAVE_SENDFILE" = x"yes"; then
        AC_DEFINE(WITH_SENDFILE,1,[Whether sendfile() should be used])
    fi
fi

dnl -- check for libgcrypt, if found enables DHX UAM
AM_PATH_LIBGCRYPT([1:1.2.3],[neta_cv_compile_dhx2=yes
			neta_cv_have_libgcrypt=yes
			AC_MSG_NOTICE([Enabling DHX2 UAM])
			AC_DEFINE(HAVE_LIBGCRYPT, 1, [Define if the DHX2 modules should be built with libgcrypt])
			AC_DEFINE(UAM_DHX2, 1, [Define if the DHX2 UAM modules should be compiled])
			])

dnl -- look for openssl, if found enables DHX UAM and Randnum UAM
AC_PATH_SSL

dnl Check for Berkeley DB library
if test "x$bdb_required" = "xyes"; then
	AC_PATH_BDB(, [
		AC_MSG_RESULT([])
		AC_MSG_RESULT([Make sure you have the required Berkeley DB libraries AND headers installed.])
		AC_MSG_RESULT([You can download the latest version from http://www.sleepycat.com.])
		AC_MSG_RESULT([If you have installed BDB in a non standard location use the])
		AC_MSG_RESULT([--with-bdb=/path/to/bdb configure option and make sure])
		AC_MSG_RESULT([your linker is configured to check for libraries there.])
		AC_MSG_ERROR([Berkeley DB library required but not found!])
	])
fi

dnl -- check for crypt
AC_CRYPT

dnl --------------------- check for building PGP UAM module

AC_MSG_CHECKING([whether the PGP UAM should be build])
AC_ARG_ENABLE(pgp-uam,
	[  --enable-pgp-uam        enable build of PGP UAM module],[
	if test "$enableval" = "yes"; then 
		if test "x$neta_cv_have_openssl" = "xyes"; then 
			AC_DEFINE(UAM_PGP, 1, [Define if the PGP UAM module should be compiled])
			compile_pgp=yes
			AC_MSG_RESULT([yes])
		else
			AC_MSG_RESULT([no])
		fi
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

dnl --------------------- check for building Kerberos v4 UAM module

AC_MSG_CHECKING([whether the Kerberos IV UAM should be build])
AC_ARG_ENABLE(krb4-uam,
	[  --enable-krb4-uam       enable build of Kerberos v4 UAM module],[
	if test "$enableval" = "yes"; then
		AC_DEFINE(UAM_KRB4, 1, [Define if the Kerberos 4 UAM module should be compiled])
		compile_kerberos=yes
		AC_MSG_RESULT([yes])
	else
		AC_MSG_RESULT([no])
	fi
	],[
		AC_MSG_RESULT([no])
	]
)

dnl --------------------- check for building Kerberos V UAM module

netatalk_cv_build_krb5_uam=no
AC_ARG_ENABLE(krbV-uam,
	[  --enable-krbV-uam       enable build of Kerberos V UAM module],
	[
		if test x"$enableval" = x"yes"; then
			NETATALK_GSSAPI_CHECK([
				netatalk_cv_build_krb5_uam=yes
			],[
				AC_MSG_ERROR([need GSSAPI to build Kerberos V UAM])
			])
		fi
	]
	
)

AC_MSG_CHECKING([whether Kerberos V UAM should be build])
if test x"$netatalk_cv_build_krb5_uam" = x"yes"; then
	AC_MSG_RESULT([yes])
else
	AC_MSG_RESULT([no])
fi
AM_CONDITIONAL(USE_GSSAPI, test x"$netatalk_cv_build_krb5_uam" = x"yes")

dnl --------------------- overwrite the config files . . . or not.

AC_MSG_CHECKING([whether configuration files should be overwritten])
AC_ARG_ENABLE(overwrite,
	[  --enable-overwrite      overwrite configuration files during installation],
	[OVERWRITE_CONFIG="${enable_overwrite}"],
	[OVERWRITE_CONFIG="no"]
)
AC_MSG_RESULT([$OVERWRITE_CONFIG])

dnl --------------------- check for ACL support
AC_MSG_CHECKING(whether to support ACLs)
AC_ARG_WITH(acls,
    [AS_HELP_STRING([--with-acls],
        [Include ACL support (default=no)])],
    [ case "$withval" in
      yes|no)
          with_acl_support="$withval"
		  ;;
      *)
          with_acl_support=no
          ;;
      esac ],
    [with_acl_support=no])
AC_MSG_RESULT($with_acl_support)

if test x"$with_acl_support" = x"no"; then
	AC_MSG_RESULT(Disabling ACL support)
	AC_DEFINE(HAVE_NO_ACLS,1,[Whether no ACLs support should be built in])
else
    with_acl_support=yes
fi

if test x"$with_acl_support" = x"yes" ; then
	AC_MSG_NOTICE(checking whether ACL support is available:)
	case "$host_os" in
	*darwin*)
		AC_MSG_NOTICE(ACLs on Darwin currently not supported)
		AC_DEFINE(HAVE_NO_ACLS,1,[Whether no ACLs support is available])
		;;
	*)
		AC_CHECK_LIB(acl,acl_get_file,[ACL_LIBS="$ACL_LIBS -lacl"])
		case "$host_os" in
		*linux*)
			AC_CHECK_LIB(attr,getxattr,[ACL_LIBS="$ACL_LIBS -lattr"])
			;;
		esac
		AC_CACHE_CHECK([for POSIX ACL support],netatalk_cv_HAVE_POSIX_ACLS,[
			acl_LIBS=$LIBS
			LIBS="$LIBS $ACL_LIBS"
			AC_TRY_LINK([
				#include <sys/types.h>
				#include <sys/acl.h>
			],[
				acl_t acl;
				int entry_id;
				acl_entry_t *entry_p;
				return acl_get_entry(acl, entry_id, entry_p);
			],
			[netatalk_cv_HAVE_POSIX_ACLS=yes],
			[netatalk_cv_HAVE_POSIX_ACLS=no
                with_acl_support=no])
			LIBS=$acl_LIBS
		])
		if test x"$netatalk_cv_HAVE_POSIX_ACLS" = x"yes"; then
			AC_MSG_NOTICE(Using POSIX ACLs)
			AC_DEFINE(HAVE_POSIX_ACLS,1,[Whether POSIX ACLs are available])
			AC_CACHE_CHECK([for acl_get_perm_np],netatalk_cv_HAVE_ACL_GET_PERM_NP,[
				acl_LIBS=$LIBS
				LIBS="$LIBS $ACL_LIBS"
				AC_TRY_LINK([
					#include <sys/types.h>
					#include <sys/acl.h>
				],[
					acl_permset_t permset_d;
					acl_perm_t perm;
					return acl_get_perm_np(permset_d, perm);
				],
				[netatalk_cv_HAVE_ACL_GET_PERM_NP=yes],
				[netatalk_cv_HAVE_ACL_GET_PERM_NP=no])
				LIBS=$acl_LIBS
			])
			if test x"$netatalk_cv_HAVE_ACL_GET_PERM_NP" = x"yes"; then
				AC_DEFINE(HAVE_ACL_GET_PERM_NP,1,[Whether acl_get_perm_np() is available])
			fi

			AC_CACHE_CHECK([for acl_from_mode], netatalk_cv_HAVE_ACL_FROM_MODE,[
				acl_LIBS=$LIBS
				LIBS="$LIBS $ACL_LIBS"
                AC_CHECK_FUNCS(acl_from_mode,
				[netatalk_cv_HAVE_ACL_FROM_MODE=yes],
				[netatalk_cv_HAVE_ACL_FROM_MODE=no])
				LIBS=$acl_LIBS
			])
			if test x"netatalk_cv_HAVE_ACL_FROM_MODE" = x"yes"; then
				AC_DEFINE(HAVE_ACL_FROM_MODE,1,[Whether acl_from_mode() is available])
			fi


		else
			AC_MSG_NOTICE(ACL support is not avaliable)
			AC_DEFINE(HAVE_NO_ACLS,1,[Whether no ACLs support is available])
		fi
		;;
    esac
fi

if test x"$with_acl_support" = x"yes" ; then
   AC_CHECK_HEADERS([acl/libacl.h])
    AC_DEFINE(HAVE_ACLS,1,[Whether ACLs support is available])
    AC_SUBST(ACL_LIBS)
fi

dnl --------------------- check for Extended Attributes support
neta_cv_eas="ad"
neta_cv_eas_sys_found=no
neta_cv_eas_sys_not_found=no

AC_CHECK_HEADERS(sys/attributes.h attr/xattr.h sys/xattr.h sys/extattr.h sys/uio.h sys/ea.h)

case "$this_os" in

  *)
	AC_SEARCH_LIBS(getxattr, [attr])

    if test "x$neta_cv_eas_sys_found" != "xyes" ; then
       AC_CHECK_FUNCS([getxattr lgetxattr fgetxattr listxattr llistxattr],
                      [neta_cv_eas_sys_found=yes],
                      [neta_cv_eas_sys_not_found=yes])
	   AC_CHECK_FUNCS([flistxattr removexattr lremovexattr fremovexattr],,
                      [neta_cv_eas_sys_not_found=yes])
	   AC_CHECK_FUNCS([setxattr lsetxattr fsetxattr],,
                      [neta_cv_eas_sys_not_found=yes])
    fi

    if test "x$neta_cv_eas_sys_found" != "xyes" ; then
	   AC_CHECK_FUNCS([getea fgetea lgetea listea flistea llistea],
                      [neta_cv_eas_sys_found=yes],
                      [neta_cv_eas_sys_not_found=yes])
	   AC_CHECK_FUNCS([removeea fremoveea lremoveea setea fsetea lsetea],,
                      [neta_cv_eas_sys_not_found=yes])
    fi

    if test "x$neta_cv_eas_sys_found" != "xyes" ; then
	   AC_CHECK_FUNCS([attr_get attr_list attr_set attr_remove],,
                      [neta_cv_eas_sys_not_found=yes])
       AC_CHECK_FUNCS([attr_getf attr_listf attr_setf attr_removef],,
                      [neta_cv_eas_sys_not_found=yes])
    fi
  ;;
esac

# Do xattr functions take additional options like on Darwin?
if test x"$ac_cv_func_getxattr" = x"yes" ; then
	AC_CACHE_CHECK([whether xattr interface takes additional options], smb_attr_cv_xattr_add_opt, [
		old_LIBS=$LIBS
		LIBS="$LIBS $ACL_LIBS"
		AC_TRY_COMPILE([
			#include <sys/types.h>
			#if HAVE_ATTR_XATTR_H
			#include <attr/xattr.h>
			#elif HAVE_SYS_XATTR_H
			#include <sys/xattr.h>
			#endif
		],[
			getxattr(0, 0, 0, 0, 0, 0);
		],
	        [smb_attr_cv_xattr_add_opt=yes],
		[smb_attr_cv_xattr_add_opt=no;LIBS=$old_LIBS])
	])
	if test x"$smb_attr_cv_xattr_add_opt" = x"yes"; then
		AC_DEFINE(XATTR_ADD_OPT, 1, [xattr functions have additional options])
	fi
fi

if test "x$neta_cv_eas_sys_found" = "xyes" ; then
   if test "x$neta_cv_eas_sys_not_found" != "xyes" ; then
      neta_cv_eas="$neta_cv_eas | sys"
   fi
fi
AC_DEFINE_UNQUOTED(EA_MODULES,["$neta_cv_eas"],[Available Extended Attributes modules])

dnl --------------------- Check if realpath() takes NULL
AC_CACHE_CHECK([if the realpath function allows a NULL argument],
    neta_cv_REALPATH_TAKES_NULL, [
        AC_TRY_RUN([
            #include <stdio.h>
            #include <limits.h>
            #include <signal.h>

            void exit_on_core(int ignored) {
                 exit(1);
            }

            main() {
                char *newpath;
                signal(SIGSEGV, exit_on_core);
                newpath = realpath("/tmp", NULL);
                exit((newpath != NULL) ? 0 : 1);
            }],
            neta_cv_REALPATH_TAKES_NULL=yes,
            neta_cv_REALPATH_TAKES_NULL=no,
            neta_cv_REALPATH_TAKES_NULL=cross
        )
    ]
)

if test x"$neta_cv_REALPATH_TAKES_NULL" = x"yes"; then
    AC_DEFINE(REALPATH_TAKES_NULL,1,[Whether the realpath function allows NULL])
fi

dnl --------------------- Netatalk Webmin
NETATALK_WEBMIN

dnl --------------------- last minute substitutions

AC_SUBST(LIBS)
AC_SUBST(CFLAGS)
AC_SUBST(OVERWRITE_CONFIG)

AM_CONDITIONAL(HAVE_LIBGCRYPT, test x$neta_cv_have_libgcrypt = xyes)
AM_CONDITIONAL(HAVE_OPENSSL, test x$neta_cv_have_openssl = xyes)
AM_CONDITIONAL(USE_DHX, test x$neta_cv_compile_dhx = xyes)
AM_CONDITIONAL(USE_DHX2, test x$neta_cv_compile_dhx2 = xyes)
AM_CONDITIONAL(USE_KERBEROS, test x$compile_kerberos = xyes)
AM_CONDITIONAL(USE_PAM_SO, test x$use_pam_so = xyes)
AM_CONDITIONAL(USE_PAM, test x$netatalk_cv_install_pam = xyes)
AM_CONDITIONAL(BUILD_PAM, test x$compile_pam = xyes)
AM_CONDITIONAL(DEFAULT_HOOK, test x$neta_cv_have_libgcrypt != xyes && test x$neta_cv_have_openssl != xyes)
AM_CONDITIONAL(USE_SHADOWPW, test x$shadowpw = xyes)
AM_CONDITIONAL(USE_UNDEF, test x$sysv_style = x)
AM_CONDITIONAL(USE_BDB, test x$bdb_required = xyes)
AM_CONDITIONAL(HAVE_ATFUNCS, test x"$ac_neta_haveatfuncs" = x"yes")

dnl --------------------- generate files

AC_OUTPUT([Makefile
	bin/Makefile
	bin/ad/Makefile
	bin/adv1tov2/Makefile
	bin/cnid/Makefile
	bin/cnid/cnid2_create
	bin/megatron/Makefile
	bin/misc/Makefile
	bin/uniconv/Makefile
	config/Makefile
	config/pam/Makefile
	contrib/Makefile
	contrib/macusers/Makefile
	contrib/macusers/macusers
	contrib/misc/Makefile
	contrib/shell_utils/Makefile
	contrib/shell_utils/apple_dump
	contrib/shell_utils/asip-status.pl
	distrib/Makefile
	distrib/config/Makefile
	distrib/config/netatalk-config
	distrib/m4/Makefile
	doc/Makefile
	etc/Makefile
	etc/afpd/Makefile
	etc/cnid_dbd/Makefile
	etc/uams/Makefile
	etc/uams/uams_krb4/Makefile
	include/Makefile
	include/atalk/Makefile
	libatalk/Makefile
	libatalk/acl/Makefile
	libatalk/adouble/Makefile
	libatalk/bstring/Makefile
	libatalk/cnid/Makefile
	libatalk/cnid/last/Makefile
	libatalk/cnid/dbd/Makefile
	libatalk/dsi/Makefile
	libatalk/util/Makefile
	libatalk/unicode/Makefile
	libatalk/unicode/charsets/Makefile
	libatalk/vfs/Makefile
	macros/Makefile
	man/Makefile
	man/man1/Makefile
	man/man3/Makefile
	man/man4/Makefile
	man/man5/Makefile
	man/man8/Makefile
	sys/Makefile
	sys/netatalk/Makefile
	test/Makefile
	test/afpd/Makefile
	],
	[chmod a+x distrib/config/netatalk-config contrib/shell_utils/apple_*]
)

AC_NETATALK_LIBS_SUMMARY
AC_NETATALK_CONFIG_SUMMARY
