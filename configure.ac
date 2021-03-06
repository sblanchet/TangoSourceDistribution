# Process this file with autoconf to produce a configure script.
# This is the configuration file for the tango system.
# To process this file into a runnable shell-script, please run
# the script `bootstrap' which is found in the same directory as
# this file.

# For extensive help on autoconf, automake, and libtool, please
# consult the infofiles by running the `info' program, or by typing
# M-x info in your favourite editor (emacs)   

# For information on how to build and use an autoconfiscated
# project, please consult
# http://sources.redhat.com/autobook/autobook/autobook.html
# which is an online version of the Gnu Autoconf, Automake, and Libtool
# book by Vaughan, Elliston, Tromey, and Taylor

# What follows is the configuration, and comments to the macros used

# AC_INIT initializes autoconf, The first argument is the
# packagename, the second is the version used for the tar file,
# the third is an email address for bug reports (shouldn't be necessary),
# and the fourth argument is                
AC_INIT(Tango,9.2.5a,info@tango-controls.org,tango)
AC_CONFIG_AUX_DIR(config)
AC_CONFIG_MACRO_DIR([m4])

# Tells autoconf that we want at least version 2.53 of autoconf to
# create configure from configure.ac
AC_PREREQ(2.53)

AM_MAINTAINER_MODE()
AC_CANONICAL_HOST()

# Lets the revision tag from cvs or rcs propagate into the
# configure script, handy for debugging.
AC_REVISION($Revision$)	

# AM_INIT_AUTOMAKE sets up automake which is used to generate
# Makefile.in from Makefile.am
AM_INIT_AUTOMAKE
AM_SILENT_RULES([yes])

# Tells automake to use `ac_config.h.tmp' to stash all the #defines
# that result from a run of configure.
# The name is .tmp since I must change the PACKAGE_ definitions because    
# of omniORBS installation of their version of this file.
AC_CONFIG_HEADERS(ac_config.h.tmp)


# -version-info CURRENT[:REVISION[:AGE]]
# From the libtool documentation (info libtool)
# So, libtool library versions are described by three integers:

# CURRENT
#      The most recent interface number that this library implements.

# REVISION
#      The implementation number of the CURRENT interface.

# AGE
#      The difference between the newest and oldest interfaces that this
#      library implements.  In other words, the library implements all the
#      interface numbers in the range from number `CURRENT - AGE' to
#      `CURRENT'.

VERSION_INFO=11:5:2
	
# Checks for programs.

AC_PROG_CXX
AC_PROG_CXXCPP
AC_PROG_CPP
AC_PROG_AWK 
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET

# Check gcc release
	 
 if test "$CXX" = "c++" -o "$CXX" = "g++"
 then
     gcc_AC_HAVE_GCC_VERSION(3,2,0)
     if test "x$ac_cv_gcc_version_3_2_0" = "xno"; then
         AC_MSG_ERROR([Not supported gcc release. Should be 3.2.0 or above. Please update !],-1)
     fi
 fi

# Check if compiler support C++11

AX_CXX_COMPILE_STDCXX_11([noext],[optional])

# Check if Java interpreter is there

AC_ARG_ENABLE([java],AC_HELP_STRING([--enable-java],[enable installation of Java applications]),
[],[enable_java=yes])

AC_ARG_ENABLE([mariadb],AC_HELP_STRING([--enable-mariadb],[enable mariadb instead of mysql]),
[enable_mariadb=yes],[enable_mariadb=no])

AC_ARG_WITH([java], 
	AC_HELP_STRING([--with-java],[path to java interpreter]),
	[JAVA=${with_java}], [with_java=yes])
	
if test "x$enable_java" = "xyes" ; then
   if test "x$with_java" = "xno" -o -z "$with_java"; then
		with_java=
		enable_java=no
   else
		if test "x$with_java" = "xyes"; then
        	AC_PATH_PROG(JAVA,java,"notfound")
		fi
		if test "x$JAVA" != "xnotfound"; then 
      		java_AC_HAVE_JAVA_VERSION($JAVA,1,7)
			if test "x$ac_cv_java_version_1_7" = "xno"; then
           		AC_MSG_WARN([Not supported java release. Should be 1.7.x or above. Java will be disabled for this build!])
				with_java=
				enable_java=no
			else
				with_java=$JAVA
			fi
		else
			with_java=
			enable_java=no
		fi
   fi
else
   with_java=
fi

# Define a conditional variable to enable or disable installation
# of java applications. The variable is used in the Makefiles.
#
 
AM_CONDITIONAL(TANGO_JAVA_ENABLED, test x"$enable_java" != x"no")

# Check if doxygen is there

AC_ARG_WITH([doxygen], 
	AC_HELP_STRING([--with-doxygen],[path to doxygen utility]),
	[DOXYGEN=${with_doxygen}],
	[AC_PATH_PROG(DOXYGEN,doxygen)])
	
if test -z "$DOXYGEN"; then
	DOXYGEN=NOT_INSTALLED
fi
      
# Check if lyx is there
	
AC_ARG_WITH([lyx], 
	AC_HELP_STRING([--with-lyx],[path to LyX utility]),
	[LYX=${with_lyx}],
	[AC_PATH_PROG(LYX,lyx)])

if test -z "$LYX"; then
	LYX="false"
fi

AM_CONDITIONAL(TANGO_LYX_ENABLED, test x"$LYX" != x"false")

# Check if fig2dev is there
	
AC_ARG_WITH([fig2dev], 
	AC_HELP_STRING([--with-fig2dev],[path to fig2dev utility]),
	[FIG2DEV=${with_fig2dev}],
	[AC_PATH_PROG(FIG2DEV,fig2dev)])

if test -z "$FIG2DEV"; then
	FIG2DEV="false"
fi

AM_CONDITIONAL(TANGO_FIG2DEV_ENABLED, test x"$FIG2DEV" != x"false")
AM_CONDITIONAL(TANGO_DOC_ENABLED, test x"$LYX" != x"false" && test x"$FIG2DEV" != x"false")


# Use libtool which facilitates the creation of shared libraries
LT_INIT()
	
# check for the presence of zlib
CHECK_ZLIB

# This macro checks for omniorb and sets various defines, please
# consult config/RSSH_CHECK_OMNIORB for more details
        
RSSH_CHECK_OMNIORB  
enable_lib=yes

# ZMQ dependency

AC_ARG_WITH(zmq, AC_HELP_STRING([--with-zmq],[prefix to ZMQ installation]) ,\
            ZMQ_PREFIX=${with_zmq} , ZMQ_PREFIX=/usr/local )

PKG_CONFIG_PATH=$PKG_CONFIG_PATH:${ZMQ_PREFIX}/lib/pkgconfig

PKG_CHECK_MODULES([LIBZMQ], [libzmq >= 4.0.5],[],AC_MSG_ERROR(Missing ZMQ library - Use --with-zmq configure option or add directory containing libzmq.pc to PKG_CONFIG_PATH environment variable))
ZMQ_VERSION=`eval pkg-config --modversion libzmq`
ZMQ_ROOT=`eval pkg-config --variable=prefix libzmq`
AC_SUBST(ZMQ_PREFIX)

# We need to define some stuff for threads and stubs

case $build_os in
    linux*)
	AC_DEFINE(_REENTRANT,1,"Needed for threads on Linux")
	CXXFLAGS="$CXXFLAGS -D_REENTRANT"
	;;
	darwin*)
	AC_DEFINE(_REENTRANT,1,"Needed for threads on darwin")
	CXXFLAGS="$CXXFLAGS -D_REENTRANT -D__darwin__"
	DARWIN=yes
esac	

# condition used in the Makefile of the Tango library
AM_CONDITIONAL(DARWIN_ENABLED,test x"$DARWIN" = x"yes" )


# we want to include the omniORB stubs into the tango library
CXXFLAGS="$CXXFLAGS -DOMNI_UNLOADABLE_STUBS"	   	


# checks for libdl
AC_CHECK_LIB(dl, dlopen)
# checks for libnsl
AC_CHECK_LIB(nsl, svc_pollfd)
# checks for libposix4
AC_CHECK_LIB(posix4, nanosleep)
# checks for  libpthread
AC_CHECK_LIB(pthread, pthread_create)
# checks for libsocket
AC_CHECK_LIB(socket, getprotobyname)

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS(fcntl.h strings.h sys/time.h unistd.h)

# Some hack for testing if we have sstream or not.			 
use_sstream=yes;
				 
if test "x$GCC" = "xyes"; then
        case `$CC --version 2>/dev/null` in
        [[12]].*) use_sstream=no ;;
        *)        use_sstream=yes ;;
        esac
fi 			 
if test "x$use_sstream" = "xyes"; then 
AC_CXX_HAVE_SSTREAM
fi
# This needs to be worked on!!

if test "x$use_sstream" = "xno"; then 
  AC_LANG_SAVE
  AC_LANG_CPLUSPLUS
  AC_CHECK_HEADER(strstream, AC_DEFINE(HAVE_STRSTREAM,1,""))
  AC_LANG_RESTORE
fi  		
# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_C_INLINE
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_HEADER_TIME
AC_STRUCT_TM

# Checks for library functions.
AC_FUNC_ALLOCA
AC_HEADER_MAJOR
AC_TYPE_SIGNAL
AC_CHECK_FUNCS(gethostname gettimeofday exit tolower)
AC_CXX_HAVE_CLASS_STRSTREAM

# check for the presence of the mysql program, needed when doing the
# install of the DataBaseds server. You can tweak the connection
# with some --with variables. ./configure --help will, uhm, help you.

AC_ARG_ENABLE([dbserver],AC_HELP_STRING([--enable-dbserver],[enable installation of Tango database server]),
              [],[enable_dbserver=yes])
enable_db_schema_create=no

AC_ARG_WITH(tango-rc-file, AC_HELP_STRING([--with-tango-rc-file],[location of the tango rc file (default /etc/tangorc)]), TANGO_RC_FILE=${with_tango_rc_file}, TANGO_RC_FILE=/etc/tangorc)
AC_DEFINE_UNQUOTED(TANGO_RC_FILE, "$TANGO_RC_FILE", The tango rc file name and location)
AC_SUBST(TANGO_RC_FILE)

# Hacks to provide the name for the tango
# database. I havent found any other way to provide these arguments.
AC_ARG_WITH(tango-db-name, AC_HELP_STRING([--with-tango-db-name],[the name of the tango database (default tango)]), TANGO_DB_NAME=${with_tango_db_name}, TANGO_DB_NAME=tango)

# Here we define these constants so that they're reachable from c programs  
AC_DEFINE_UNQUOTED(TANGO_DB_NAME, "$TANGO_DB_NAME", The tango database name)
			
# Here we propagate the constants into the makefiles.
AC_SUBST(TANGO_DB_NAME)			

if test "x$enable_mariadb" != "xyes" ; then
	DB_NAME=MYSQL
	AS_IF([test "x$enable_dbserver" = "xyes"], [ 

	# We need libmysqlclient to compile
	# finds it for us. Abort if no mysqlclientlibs are found

		AM_PATH_MYSQLCLIENT
		if test "x$MYSQLCLIENT_CFLAGS" = "x"
			then
  		 		AC_MSG_WARN(No libmysqlclient libs found)
				enable_dbserver=no
			fi

		DB_LDFLAGS=${MYSQLCLIENT_LDFLAGS}
		DB_LDLIBS=${MYSQLCLIENT_LIBS}
		DB_CFLAGS=${MYSQLCLIENT_CFLAGS}
	
		AC_PATH_PROG(MYSQL, mysql, nocommand)
		MYSQL_VERSION=not_found
	
		if test "x$MYSQL" != "xnocommand"; then
			mysql_AC_HAVE_MYSQL_VERSION($MYSQL,5,5)
			if test "x$ac_cv_mysql_version_5_5" = "xno"; then
			 	AC_MSG_WARN([Not supported mysql release. Should be 5.5.x or above. Please update !],-1)
				enable_db_schema_create=no
				enable_dbserver=no
			else
				enable_db_schema_create=yes
			fi
		else
			enable_db_schema_create=no
		fi
 	

		# check for a database schema update
	
		AS_IF([test "x$enable_db_schema_create" = "xyes"], [
	
			AC_ARG_ENABLE([dbcreate],AC_HELP_STRING([--enable-dbcreate],[enable an creation of the Tango database schema]),
		             		[],[enable_dbcreate=yes])
			enable_db_schema_create=$enable_dbcreate
			 			  
			AS_IF([test "x$enable_db_schema_create" = "xyes"], [  
	
				AC_PROG_MYSQL

			])
		])
	])
	if test "x$MYSQL_VERSION" != "xnot_found"; then
		DB_VERSION=${MYSQL_VERSION}
	fi
else
	DB_NAME=MARIADB
	AS_IF([test "x$enable_dbserver" = "xyes"], [ 

		# We need libmysqlclient to compile
		# finds it for us. Abort if no mysqlclientlibs are found

		AM_PATH_MARIADBCLIENT
		if test "x$MARIADBCLIENT_CFLAGS" = "x"
			then
	  		 	AC_MSG_WARN(No libmariadbclient libs found)
				enable_dbserver=no
			fi

		DB_LDFLAGS=${MARIADBCLIENT_LDFLAGS}
		DB_LDLIBS=${MARIADBCLIENT_LIBS}
		DB_CFLAGS=${MARIADBCLIENT_CFLAGS}
	
		AC_PATH_PROG(MYSQL, mysql, nocommand)
		MARIADB_VERSION=not_found
	
		if test "x$MYSQL" != "xnocommand"; then
			mariadb_AC_HAVE_MARIADB_VERSION($MYSQL,10,0)
			if test "x$ac_cv_mariadb_version_10_0" = "xno"; then
			 	AC_MSG_WARN([Not supported mariadb release. Should be 10.0.x or above. Please update !],-1)
				enable_db_schema_create=no
				enable_dbserver=no
			else
				enable_db_schema_create=yes
			fi
		else
			enable_db_schema_create=no
		fi
	 	

		# check for a database schema update

		AS_IF([test "x$enable_db_schema_create" = "xyes"], [
	
			AC_ARG_ENABLE([dbcreate],AC_HELP_STRING([--enable-dbcreate],[enable an creation of the Tango database schema]),
		             		[],[enable_dbcreate=yes])
			enable_db_schema_create=$enable_dbcreate
			 			  
			AS_IF([test "x$enable_db_schema_create" = "xyes"], [  
	
				AC_PROG_MYSQL

			])
		])
	])
	if test "x$MARIADB_VERSION" != "xnot_found"; then
		DB_VERSION=${MARIADB_VERSION}
	fi
fi


AC_SUBST(DB_LDFLAGS)
AC_SUBST(DB_CFLAGS)
AC_SUBST(DB_LDLIBS)

#
# Build the jpeg library compilation option
#

AC_ARG_ENABLE([jpegmmx],
	[AS_HELP_STRING([--disable-jpegmmx],[disable jpeg mmx optimization])],
	[],[enable_jpegmmx=yes])

JPEG_LIB_CXXFLAGS="-D_TANGO_LIB"
JPEG_MMX_LIB_CXXFLAGS="-D_TANGO_LIB"

if test "x$enable_jpegmmx" != xno; then
case $host_cpu in
	i*86 )
		JPEG_LIB_CXXFLAGS="-D_TANGO_LIB -DJPG_USE_ASM"
		JPEG_MMX_LIB_CXXFLAGS="-D_TANGO_LIB -mmmx -DJPG_USE_ASM"
	;;
esac
fi

AC_SUBST(JPEG_LIB_CXXFLAGS)
AC_SUBST(JPEG_MMX_LIB_CXXFLAGS)

# Define a conditional variables to enable or disable installation
# of the database server and the database schema creation. 
# The variables are used in the Makefiles.
#

AM_CONDITIONAL(TANGO_DB_SERVER_ENABLED, test x"$enable_dbserver" = x"yes")
AM_CONDITIONAL(TANGO_DB_CREATE_ENABLED, test x"$enable_db_schema_create" = x"yes")
	
	
						   	
AC_SUBST(ac_aux_dir)	
AC_SUBST(VERSION_INFO)

DATADIR=`eval echo $datadir`
DATADIR=`eval echo $DATADIR`
AC_SUBST(DATADIR)

AC_SUBST(JAVA)
AC_SUBST(DOXYGEN)
AC_SUBST(LYX)
AC_SUBST(FIG2DEV)
AC_SUBST(JNI_INCL_DIRS)

# This is a hack to change PACKAGE_ #defines into TANGO_PACKAGE
AC_CONFIG_COMMANDS(ac_config.h,
		   sed s/PACKAGE/TANGO_PACKAGE/g <ac_config.h.tmp>ac_config.h)

# configure log4tango sub-package
AC_CONFIG_SUBDIRS(lib/cpp/log4tango)

# All the Makefiles to be generated.
AC_OUTPUT(Makefile
	  lib/Makefile
	  lib/idl/Makefile
	  lib/cpp/tango.pc
	  lib/cpp/Makefile
	  lib/cpp/server/Makefile
	  lib/cpp/server/idl/Makefile
	  lib/cpp/server/jpeg/Makefile
	  lib/cpp/server/jpeg_mmx/Makefile
	  lib/cpp/client/Makefile
	  lib/cpp/client/helpers/Makefile
	  lib/java/Makefile
	  cppserver/Makefile
	  cppserver/database/Makefile
	  cppserver/database/create_db.sql	
	  cppserver/database/create_db.sh
	  cppserver/database/my.cnf
	  cppserver/database/stored_proc.sql
	  cppserver/database/create_db_tables.sql
	  cppserver/database/update_db.sh
	  cppserver/database/update_db.sql
	  cppserver/database/update_db8.sql
	  cppserver/database/update_db7.sql
      cppserver/database/rem_history.sql
	  cppserver/starter/Makefile
  	  cppserver/tangotest/Makefile
	  cppserver/AbstractClass/Makefile
	  cppserver/AbstractClass/AccessControl/Makefile
	  cppserver/tangoaccesscontrol/Makefile
	  utils/Makefile
	  utils/tango_admin/Makefile
	  scripts/Makefile
	  scripts/tango
	  scripts/tango_wca
  	  doc/Makefile
	  doc/man/Makefile
	  doc/src/Makefile
	  doc/src/ds_writing/Makefile
	  doc/src/ds_model/Makefile
	  doc/src/dance/Makefile
	  doc/src/java_api/Makefile
	  doc/src/java_api/picture/Makefile
	  doc/src/gen_api/Makefile
	  doc/src/advanced/Makefile
	  pogo/Makefile
	  pogo/templates/Makefile
	  pogo/preferences/Makefile
	  pogo/templates/cpp/Makefile)


AC_MSG_RESULT([
Configuration ($PACKAGE):

	Source code location:   ${srcdir}
	Version:                ${VERSION}
	Compiler:               ${ac_ct_CC},${ac_ct_CXX}

	OMNIORB PATH:           ${OMNI_ROOT} 
	OMNIORB VERSION:        ${OMNI_VERSION}

	ZMQ PATH:               ${ZMQ_ROOT}
	ZMQ VERSION:            ${ZMQ_VERSION}
	
	JAVA PATH:              ${with_java}
	JAVA VERSION:           ${JAVA_VERSION}
		
	${DB_NAME} CLIENT LIB:  ${DB_LDFLAGS} ${DB_LDLIBS}
	${DB_NAME} VERSION:     ${DB_VERSION}
	${DB_NAME} CONNECTION:  ${MYSQL_CONNECTION}
		
build:
	libraries:              ${enable_lib}
	java application:       ${enable_java}
	access control server:	${enable_dbserver}
	database server:        ${enable_dbserver}
	database schema create: ${enable_db_schema_create}
	
Please check whether the configuration I detected matches what you
would like to have.

])
