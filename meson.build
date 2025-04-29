# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# information on the package
# ---------------------------

project('sofia-sip', 'c', version: '1.13.17')

cc = meson.get_compiler('c')
conf = configuration_data()
toplevel_inc = include_directories('.')

VER_LIBSOFIA_SIP_UA_MAJOR_MINOR = '1.13'
# Includedir specific to this sofia version
include_sofiadir = f'sofia-sip-@VER_LIBSOFIA_SIP_UA_MAJOR_MINOR@'
LIBVER_SOFIA_SIP_UA_CUR = 6
LIBVER_SOFIA_SIP_UA_REV = 0
LIBVER_SOFIA_SIP_UA_AGE = 6
LIBVER_SOFIA_SIP_UA_SOVER = LIBVER_SOFIA_SIP_UA_CUR - LIBVER_SOFIA_SIP_UA_AGE
LIBVER_SOFIA_SIP_UA_GLIB_CUR = 3
LIBVER_SOFIA_SIP_UA_GLIB_REV = 0
LIBVER_SOFIA_SIP_UA_GLIB_AGE = 0
LIBVER_SOFIA_SIP_UA_GLIB_SOVER = LIBVER_SOFIA_SIP_UA_GLIB_CUR - LIBVER_SOFIA_SIP_UA_GLIB_AGE

add_project_arguments('-DSU_DEBUG=0', language: 'c')

AWK = find_program('awk')
DOXYGEN = find_program('doxygen', required: false)

SOFIA_PLAT_CFLAGS = []
# openbsd seems to not define NULL as a void pointer, I blame standards by committee for this.
# This is a dirty hack, but shuts up all the warnings
if host_machine.system() == 'openbsd'
  SOFIA_PLAT_CFLAGS = ['-DNULL=\'(void *) 0L\'']
endif

### checks for header files
### -----------------------

# AC_HEADER_STDC
# TODO: Make this more portable.
AC_INCLUDES_DEFAULT = '''
#include <stdio.h>
#if __has_include(<sys/types.h>)
# include <sys/types.h>
#endif
#if __has_include(<sys/stat.h>)
# include <sys/stat.h>
#endif
#include <stdlib.h>
#include <stddef.h>
#if __has_include(<string.h>)
# include <string.h>
#endif
#if __has_include(<strings.h>)
# include <strings.h>
#endif
#if __has_include(<inttypes.h>)
# include <inttypes.h>
#endif
#if __has_include(<stdint.h>)
# include <stdint.h>
#endif
#if __has_include(<unistd.h>)
# include <unistd.h>
#endif
'''

### checks for types
### ----------------

# AC_TYPE_SIGNAL
conf.set('RETSIGTYPE', 'void')

# AC_TYPE_LONGLONG
# ======================================================================
# Find long long (at least 64 bits)
# ======================================================================

if cc.has_type('long long')
  conf.set('longlong', 'long long',
           description: 'Define to a at least 64-bit int type')
endif

# Define HAVE_C99_FORMAT to 1 if the formatted IO functions (printf/scanf
# et.al.) support the C99 'size specifiers', namely ll, hh, j, z, t
# (representing long long int, char, intmax_t, size_t, ptrdiff_t). Some C
# compilers supported these specifiers prior to C99 as an extension.

ac_cv_c_c99_format = cc.run(AC_INCLUDES_DEFAULT + '''
int main(void) {
char buf[64];
if (sprintf(buf, "%lld%hhd%jd%zd%td", (long long int)1, (char)2, (intmax_t)3, (size_t)4, (ptrdiff_t)5) != 5)
  return 1;
else if (strcmp(buf, "12345"))
  return 2;
}
''',
  name: 'IO functions support C99 size specifiers')

if ac_cv_c_c99_format.compiled() and ac_cv_c_c99_format.returncode() == 0
  conf.set10('HAVE_C99_FORMAT', true,
             description: 'Define to 1 if printf supports C99 size specifiers')

  conf.set('LLU', '"%llu"',
           description: 'Format (%llu) for unsigned long long')
  conf.set('LLI', '"%lli"',
           description: 'Format (%lli) for long long')
  conf.set('LLX', '"%llx"',
           description: 'Format (%llx) for long long hex')
  conf.set('MOD_ZD', '"%zd"',
           description: 'Define printf() modifier for ssize_t')
  conf.set('MOD_ZU', '"%zu"',
           description: 'Define printf() modifier for size_t')

else

  ac_cv_c_ll_format = cc.run(AC_INCLUDES_DEFAULT + '''
int main(void) {
char buf[64];
if (sprintf(buf, "%lld", (long long int)1) != 1)
  return 1;
else if (strcmp(buf, "1"))
  return 2;
}
''',
    name: 'IO functions support size specifier for long long')

  if ac_cv_c_ll_format.compiled() and ac_cv_c_ll_format.returncode() == 0
    conf.set('LLU', '"%llu"',
             description: 'Format (%llu) for unsigned long long')
    conf.set('LLI', '"%lli"',
             description: 'Format (%lli) for long long')
    conf.set('LLX', '"%llx"',
             description: 'Format (%llx) for long long hex')
  else
    error('printf cannot handle 64-bit integers')
  endif

  ac_cv_c_z_format = cc.run(AC_INCLUDES_DEFAULT + '''
int main(void) {
char buf[64];
if (sprintf(buf, "%zd", (size_t)1) != 1)
  return 1;
else if (strcmp(buf, "1"))
  return 2;
}
''',
    name: 'IO functions support size specifier for size_t')

  if ac_cv_c_z_format.compiled() and ac_cv_c_z_format.returncode() == 0
    conf.set('MOD_ZD', '"%ld"',
             description: 'Define printf() modifier for ssize_t')
    conf.set('MOD_ZU', '"%lu"',
             description: 'Define printf() modifier for size_t')
  else
    # Cross fingers
    warning('printf cannot handle size_t, using long instead')
    conf.set('MOD_ZD', '"%ld"',
             description: 'Define printf() modifier for ssize_t')
    conf.set('MOD_ZU', '"%lu"',
             description: 'Define printf() modifier for size_t')
  endif
endif

### checks for typedefs, structures, and compiler characteristics.
### --------------------------------------------------------------
# AC_C_BIGENDIAN
if host_machine.endian() == 'big'
  conf.set('SWITCH_BYTE_ORDER', '__BIG_ENDIAN')
else
  conf.set('SWITCH_BYTE_ORDER', '__LITTLE_ENDIAN')
endif

# AC_C_VAR_FUNC
ac_cv_c_var_func = cc.compiles(
  'char *s = __func__;',
  name: '$CC recognizes __func__')
conf.set10('HAVE_FUNC', ac_cv_c_var_func,
           description: 'Define to 1 if the C compiler supports __func__')

# AC_C_MACRO_FUNCTION
ac_cv_c_macro_function = cc.compiles(
  'char *s = __FUNCTION__;',
  name: '$CC recognizes __FUNCTION__')
conf.set10('HAVE_FUNCTION', ac_cv_c_macro_function,
           description: 'Define to 1 if the C compiler supports __FUNCTION__')

# AC_C_KEYWORD_STRUCT
ac_cv_c_keyword_struct = cc.compiles('''
int main(void) {
  struct { int foo; char *bar; } test = { foo: 1, bar: "bar" };
  return 0;
}''',
  name: '$CC recognizes field names in struct initialization')
conf.set10('HAVE_STRUCT_KEYWORDS', ac_cv_c_keyword_struct,
           description: 'Define to 1 if your CC supports C99 struct initialization')

# AC_HEADER_TIME
conf.set10('HAVE_SYS_TIME_H', cc.check_header('sys/time.h'))
conf.set10('TIME_WITH_SYS_TIME', true)

### checks for system services
### --------------------------

# AC_DEV_URANDOM
# ======================================================================
# Check for /dev/urandom
# ======================================================================

fs = import('fs')
conf.set10('HAVE_DEV_URANDOM', fs.exists('/dev/urandom'),
           description: 'Define to 1 if you have /dev/urandom.')

### checks for libraries
### --------------------

## ======================================================================
## Check for features used by su

## Define compilation options for su_configure.h

if host_machine.system() == 'sunos'
  conf.set10('__EXTENSIONS__', true,
             description: 'Define to 1 in Solaris in order to get POSIX extensions.')
endif

## Check includes used by su includes
conf.set10('SU_HAVE_SYS_TYPES', cc.check_header('sys/types.h'),
           description: 'Define to 1 if Sofia uses sys/types.h')

conf.set10('SU_HAVE_STDINT', cc.check_header('stdint.h'),
           description: 'Define to 1 if Sofia uses stdint.h')
conf.set10('SU_HAVE_INTTYPES', cc.check_header('inttypes.h'),
           description: 'Define to 1 if Sofia uses inttypes.h')
if conf.get('SU_HAVE_STDINT') == 0 and conf.get('SU_HAVE_INTTYPES') == 0
  error('No <stdint.h> or <inttypes.h> found.')
endif

pthread_dep = dependency('threads', required: false)
conf.set10('SU_HAVE_PTHREADS', pthread_dep.found(),
           description: 'Sofia SU uses pthreads')

conf.set10('SU_HAVE_EXPERIMENTAL', get_option('experimental'),
           description: 'Enable experimental features')

## ===========================================================================
## Checks for typedefs, headers, structures, and compiler characteristics.
## ===========================================================================

# AC_REQUIRE([AC_C_CONST]) - This is obsolescent, so not bothering.

# AC_REQUIRE([AC_TYPE_SIZE_T])
if not cc.has_type('size_t', prefix: AC_INCLUDES_DEFAULT)
  conf.set('size_t', 'unsigned int',
           description: 'Define as \'unsigned int\' if <stddef.h> doesn\'t define.')
endif

# AC_REQUIRE([AC_C_INLINE])
ac_cv_c_inline = false
foreach kw : ['inline', '__inline__', '__inline']
  if cc.compiles(f'''
#ifndef __cplusplus
typedef int foo_t;
static @kw@ foo_t static_foo (void) {return 0; }
@kw@ foo_t foo (void) {return 0; }
#endif
''')
    ac_cv_c_inline = kw
    break
  endif
endforeach

if ['inline', true].contains(ac_cv_c_inline)
  conf.set('su_inline', 'static inline',
           description: 'Define to declarator for static inline functions.')
  conf.set('SU_INLINE', 'inline',
           description: 'Define to declarator for inline functions.')
  conf.set10('SU_HAVE_INLINE', true,
             description: 'Define to 1 if you have inline functions.')
  conf.set10('SU_INLINE_TAG_CAST', get_option('tag-cast'),
             description: 'Define to 1 if you use inline function to cast tag values.')
elif [false].contains(ac_cv_c_inline)
  conf.set('su_inline', 'static')
  conf.set('SU_INLINE', '/*inline*/')
  conf.set10('SU_HAVE_INLINE', false)
  conf.set10('SU_INLINE_TAG_CAST', false)
else
  conf.set('su_inline', f'static @ac_cv_c_inline@')
  conf.set('SU_INLINE', ac_cv_c_inline)
  conf.set10('SU_HAVE_INLINE', true)
  conf.set10('SU_INLINE_TAG_CAST', get_option('tag-cast'))
endif

if not get_option('size-compat')
  conf.set('SOFIA_ISIZE_T', 'size_t')
  conf.set('ISIZE_MAX', 'SIZE_MAX')
  conf.set('SOFIA_ISSIZE_T', 'ssize_t')
  conf.set('ISSIZE_MAX', 'SSIZE_MAX')
  conf.set('SOFIA_USIZE_T', 'size_t')
  conf.set('USIZE_MAX', 'SIZE_MAX')
else
  conf.set('SOFIA_ISIZE_T', 'int')
  conf.set('ISIZE_MAX', 'INT_MAX')
  conf.set('SOFIA_ISSIZE_T', 'int')
  conf.set('ISSIZE_MAX', 'INT_MAX')
  conf.set('SOFIA_USIZE_T', 'unsigned')
  conf.set('USIZE_MAX', 'UINT_MAX')
endif

## ======================================================================
## SAC_ENABLE_COREFOUNDATION
## ======================================================================
COREFOUNDATION = get_option('corefoundation')

conf.set10('SU_HAVE_OSX_CF_API', COREFOUNDATION,
           description: 'Define as 1 if you have OSX CoreFoundation interface')
if COREFOUNDATION
  # TODO: Use this somewhere.
  dependency('appleframeworks', modules: ['CoreFoundation', 'SystemConfiguration'])
endif

## ======================================================================
## Test if we have stack suitable for handling tags directly
##

ac_cv_tagstack = cc.run('''
#if __has_include(<inttypes.h>)
#include <inttypes.h>
#endif
#if __has_include(<stdint.h>)
#include <stdint.h>
#endif

#include <stdarg.h>

typedef void *tp;
typedef intptr_t tv;

int test1(tv l, tv h, ...)
{
  va_list ap;
  tv i, *p = &l;

  va_start(ap, h);

  if (*p++ != l || *p++ != h) return 1;

  for (i = l; i <= h; i++) {
    if (*p++ != i)
      return 1;
  }

  for (i = l; i <= h; i++) {
    if (va_arg(ap, tv) != i)
      return 1;
  }

  va_end(ap);

  return 0;
}

int main(int avc, char **av)
{
  return test1((tv)1, (tv)10,
	       (tv)1, (tv)2, (tv)3, (tv)4, (tv)5,
	       (tv)6, (tv)7, (tv)8, (tv)9, (tv)10);
}
''',
  name: 'stack suitable for tags')

conf.set10('SU_HAVE_TAGSTACK',
           ac_cv_tagstack.compiled() and ac_cv_tagstack.returncode() == 0,
           description: 'Define this as 1 if your compiler puts the variable argument list nicely in memory')

## ======================================================================
## Test if free(0) fails
##

ac_cv_free_null = cc.run('''
#include <stdlib.h>

int main(int avc, char **av)
{
	free(0);
	return 0;
}
''',
  name: 'graceful free(0)')

conf.set10('HAVE_FREE_NULL',
           ac_cv_free_null.compiled() and ac_cv_free_null.returncode() == 0,
           description: 'Define this as 1 if your c library does not crash with free(0)')

## ======================================================================
## Socket features

# AC_SYS_SA_LEN
# ======================================================================
# Check for sa_len in struct sockaddr
# ======================================================================
ac_cv_sa_len = cc.has_member('struct sockaddr', 'sa_len', required: false,
                             prefix: ['#include <sys/types.h>', '#include <sys/socket.h>'])
conf.set10('HAVE_SA_LEN', ac_cv_sa_len,
           description: 'Define to 1 if you have sa_len in struct sockaddr')
conf.set10('SU_HAVE_SOCKADDR_SA_LEN', ac_cv_sa_len,
           description: 'Define to 1 if you have sa_len in struct sockaddr')

if get_option('ip6')
  # AC_STRUCT_SIN6
  # ======================================================================
  # Check for sockaddr_in6
  # ======================================================================
  ac_cv_sin6 = cc.has_header_symbol('netinet/in.h', 'struct sockaddr_in6')
  conf.set10('HAVE_SIN6', ac_cv_sin6,
             description: 'Define to 1 if you have IPv6 structures and constants')
  conf.set10('SU_HAVE_IN6', ac_cv_sin6,
             description: 'Define to 1 if you have struct sockaddr_in6')
endif

foreach header: ['unistd.h', 'sys/time.h', 'fcntl.h', 'dirent.h']
  name = header.underscorify().to_upper()
  conf.set10(f'HAVE_@name@', cc.check_header(header, required: false))
endforeach

if cc.check_header('winsock2.h', required: false)
  conf.set10('HAVE_WIN32', true,
             description: 'Define to 1 you have WIN32')
  conf.set10('SU_HAVE_WINSOCK', true,
             description: 'Define to 1 you have WinSock')
  conf.set10('SU_HAVE_WINSOCK2', true,
             description: 'Define to 1 you have WinSock2')
  conf.set10('SU_HAVE_SOCKADDR_STORAGE', true,
             description: 'Define to 1 if you have struct sockaddr_storage')
  conf.set10('HAVE_ADDRINFO', true,
             description: 'Define to 1 if you have addrinfo structure.')
  conf.set10('HAVE_GETADDRINFO', true,
             description: 'Define to 1 if you have addrinfo structure.')
  conf.set10('HAVE_FREEADDRINFO', true,
             description: 'Define to 1 if you have addrinfo structure.')
  conf.set10('SU_HAVE_ADDRINFO', true,
             description: 'Define to 1 if you have addrinfo structure.')
  foreach header: ['windef.h', 'ws2tcpip.h']
    name = header.underscorify().to_upper()
    conf.set10(f'HAVE_@name@', cc.check_header(header, required: false))
  endforeach

  if cc.check_header('iphlpapi.h', required: false,
                     prefix: conf.get('HAVE_WINDEF_H') == 1 ? ['#include <windef.h>', '#include <winbase.h>'] : [])
    conf.set10('HAVE_INTERFACE_INFO_EX', true,
               description: 'Define to 1 if you have WIN32 INTERFACE_INFO_EX type.')
    conf.set10('HAVE_SIO_ADDRESS_LIST_QUERY', true,
               description: 'Define to 1 if you have WIN32 WSAIoctl SIO_ADDRESS_LIST_QUERY.')
  endif
  conf.set10('HAVE_FILETIME', true,
             description: 'Define to 1 if you have WIN32 FILETIME type and GetSystemTimeAsFileTime().')
else
## no winsock2

  conf.set10('SU_HAVE_BSDSOCK', true,
             description: 'Define to 1 if you have BSD socket interface')
  foreach header: ['sys/socket.h', 'sys/ioctl.h', 'sys/filio.h', 'sys/sockio.h', 'sys/select.h', 'sys/epoll.h', 'sys/devpoll.h']
    name = header.underscorify().to_upper()
    conf.set10(f'HAVE_@name@', cc.check_header(header, required: false))
  endforeach
  foreach header: ['netinet/in.h', 'arpa/inet.h', 'netdb.h', 'net/if.h', 'net/if_types.h', 'ifaddr.h', 'netpacket/packet.h']
    name = header.underscorify().to_upper()
    conf.set10(f'HAVE_@name@',
      cc.check_header(header, required: false,
        prefix: ['#include <sys/types.h>', '#include <sys/socket.h>']))
  endforeach

  conf.set10('HAVE_MSG_TRUNC',
    cc.has_header_symbol(
      'sys/socket.h', 'MSG_TRUNC', prefix: '#include <sys/types.h>'),
    description: 'Define to 1 if you have MSG_TRUNC flag')

  conf.set10('HAVE_SO_RCVBUFFORCE',
    cc.has_header_symbol(
      'sys/socket.h', 'SO_RCVBUFFORCE', prefix: '#include <sys/types.h>'),
    description: 'Define to 1 if you have socket option SO_RCVBUFFORCE')

  conf.set10('HAVE_SO_SNDBUFFORCE',
    cc.has_header_symbol(
      'sys/socket.h', 'SO_SNDBUFFORCE', prefix: '#include <sys/types.h>'),
    description: 'Define to 1 if you have socket option SO_SNDBUFFORCE')

  conf.set10('HAVE_IP_ADD_MEMBERSHIP',
    cc.has_header_symbol(
      'netinet/in.h', 'IP_ADD_MEMBERSHIP',
      prefix: ['#include <sys/types.h>', '#include <sys/socket.h>']),
    description: 'Define to 1 if you have IP_ADD_MEMBERSHIP')

  conf.set10('HAVE_IP_MULTICAST_LOOP',
    cc.has_header_symbol(
      'netinet/in.h', 'IP_MULTICAST_LOOP',
      prefix: ['#include <sys/types.h>', '#include <sys/socket.h>']),
    description: 'Define to 1 if you have IP_MULTICAST_LOOP')

  conf.set10('HAVE_IP_MTU_DISCOVER',
    cc.has_header_symbol(
      'netinet/in.h', 'IP_MTU_DISCOVER',
      prefix: ['#include <sys/types.h>', '#include <sys/socket.h>']),
    description: 'Define to 1 if you have IP_MTU_DISCOVER')

  conf.set10('SU_HAVE_ADDRINFO',
    cc.has_header_symbol('netdb.h', 'struct addrinfo'),
    description: 'Define to 1 if you have struct addrinfo.')

  conf.set10('SU_HAVE_SOCKADDR_STORAGE',
    cc.has_header_symbol('sys/socket.h', 'struct sockaddr_storage'),
    description: 'Define to 1 if you have struct sockaddr_storage.')

  conf.set10('HAVE_IFR_INDEX',
    cc.has_member(
      'struct ifreq',
      'ifr_index',
      prefix: [
        '#include <sys/types.h>',
        '#include <arpa/inet.h>',
        '#include <netdb.h>',
        '#include <sys/socket.h>',
        '#include <net/if.h>',
      ]),
    description: 'Define to 1 if you have ifr_index in <net/if.h>')
  if conf.get('HAVE_IFR_INDEX') == 0
    conf.set10('HAVE_IFR_IFINDEX',
      cc.has_member(
        'struct ifreq',
        'ifr_ifindex',
        prefix: [
          '#include <sys/types.h>',
          '#include <arpa/inet.h>',
          '#include <netdb.h>',
          '#include <sys/socket.h>',
          '#include <net/if.h>',
        ]),
      description: 'Define to 1 if you have ifr_ifindex in <net/if.h>')
  endif

  # SIOGCIFCONF & struct ifconf
  conf.set10('HAVE_IFCONF', cc.has_header_symbol('net/if.h', 'struct ifconf'),
             description: 'Define to 1 if you have SIOCGIFCONF')

  conf.set10('HAVE_IFNUM', cc.has_header_symbol('sys/sockio.h', 'SIOCGIFNUM'),
             description: 'Define to 1 if you have SIOCGIFNUM ioctl')

endif  # cc.check_header('winsock2.h')

## ===========================================================================
## Checks for libraries
## ===========================================================================

LIBS = []  # TODO: Actually use this.

pthread = cc.find_library('pthread', required: false)
if pthread.found() and cc.has_function('pthread_create', dependencies: pthread)
  conf.set10('HAVE_LIBPTHREAD', true)
  LIBS += pthread
else
  conf.set10('HAVE_LIBPTHREAD', false)
endif
socket = cc.find_library('socket', required: false)
nsl = cc.find_library('nsl', required: false)
if socket.found() and cc.has_function('socketpair', dependencies: [socket, nsl])
  conf.set10('HAVE_LIBSOCKET', true)
  LIBS += [socket, nsl]
else
  conf.set10('HAVE_LIBSOCKET', false)
endif

if get_option('rt')
  rt = cc.find_library('rt', required: false)
  conf.set10('HAVE_CLOCK_GETTIME', cc.has_function('clock_gettime', dependencies: [rt]))
  if conf.get('HAVE_CLOCK_GETTIME') == 1
    LIBS += rt
  else
    conf.set10('HAVE_CLOCK_GETTIME', cc.has_function('clock_gettime'))
  endif
  conf.set10('HAVE_CLOCK_GETCPUCLOCKID', cc.has_function('clock_getcpuclockid'))
  conf.set10('HAVE_CLOCK_MONOTONIC',
    cc.has_define('CLOCK_MONOTONIC', prefix: '#include <time.h>'),
    description: 'Define to 1 if you have CLOCK_MONOTONIC')
endif

glib_dep = dependency('glib-2.0', required: get_option('glib'))

## ===========================================================================
## Checks for library functions.
## ===========================================================================

library_functions = {
  'getaddrinfo': ['xnet', 'socket', 'nsl'],
  'gethostbyname': ['xnet', 'nsl'],
  'getipnodebyname': ['xnet', 'socket', 'nsl'],
  'inet_ntop': ['socket', 'nsl'],
  'inet_pton': ['socket', 'nsl'],
  'socket': ['xnet', 'socket'],
}

foreach func, alternates : library_functions
  name = func.underscorify().to_upper()
  conf.set10(f'HAVE_@name@', false)
  if cc.has_function(func)
    conf.set10(f'HAVE_@name@', true)
  else
    foreach alternate : alternates
      altlib = cc.find_library(alternate, required: false)
      if altlib.found() and cc.has_function(func, dependencies: altlib)
        LIBS += altlib
        conf.set10(f'HAVE_@name@', true)
        break
      endif
    endforeach
  endif
endforeach

conf.set10('HAVE_ALLOCA_H', cc.check_header('alloca.h'))

foreach func : ['gettimeofday', 'strerror', 'random', 'initstate', 'tcsetattr', 'flock',
                'socketpair', 'gethostname', 'gethostbyname', 'getipnodebyname',
                'poll', 'epoll_create', 'kqueue', 'select', 'if_nameindex',
                'signal', 'alarm',
                'strnlen',
                'getaddrinfo', 'getnameinfo', 'freeaddrinfo', 'gai_strerror', 'getifaddrs',
                ## getline getdelim getpass are _gnu_source stuff
                'getline', 'getdelim', 'getpass',
                # Possible substitutions in su/.
                'memmem', 'memccpy', 'memspn', 'memcspn', 'strtoull', 'inet_ntop', 'inet_pton', 'poll']
  name = func.underscorify().to_upper()
  conf.set10(f'HAVE_@name@', cc.has_function(func))
endforeach

conf.set10('SU_HAVE_POLL', conf.get('HAVE_POLL') == 1,
           description: 'Define to 1 if you have poll().')

conf.set10('HAVE_EPOLL',
           conf.get('HAVE_EPOLL_CREATE') == 1 or conf.get('HAVE_SYS_EPOLL_H', 0) == 1,
           description: 'Define to 1 if you have epoll interface.')

conf.set10('SU_HAVE_IF_NAMEINDEX', conf.get('HAVE_IF_NAMEINDEX') == 1,
           description: 'Define to 1 if you have if_nameindex().')

conf.set10(
  'HAVE_SIGPIPE',
  conf.get('HAVE_SIGNAL') == 1 and cc.has_define('SIGPIPE', prefix: '#include <signal.h>'),
  description: 'Define to 1 if you have SIGPIPE')
# also add SIGHUP SIGQUIT if needed

## ===========================================================================
## Check how to implement su_port
## ===========================================================================

enable_poll_port = get_option('poll-port')
if enable_poll_port == 'maybe'
  if conf.get('HAVE_POLL') == 1
    enable_poll_port = 'yes'
  endif
endif
conf.set10('HAVE_POLL_PORT', enable_poll_port == 'yes',
           description: 'Define to 1 if you use poll in su_port.')

## ===========================================================================
## Check pthread_rwlock_unlock()
## ===========================================================================

if pthread_dep.found()
  ac_cv_pthread_rwlock = cc.run('''
#define _XOPEN_SOURCE (500)

#include <pthread.h>

pthread_rwlock_t rw;

int main(void)
{
  pthread_rwlock_init(&rw, NULL);
  pthread_rwlock_rdlock(&rw);
  pthread_rwlock_rdlock(&rw);
  pthread_rwlock_unlock(&rw);
  /* pthread_rwlock_trywrlock() should fail (not return 0) */
  return pthread_rwlock_trywrlock(&rw) != 0 ? 0  : 1;
}
''',
  dependencies: pthread,
  name: 'pthread_rwlock works')

  conf.set10('HAVE_PTHREAD_RWLOCK',
             ac_cv_pthread_rwlock.compiled() and ac_cv_pthread_rwlock.returncode() == 0,
             description: '''Define to 1 if you have working pthread_rwlock_t implementation.

   A  thread  may hold multiple concurrent read locks on rwlock - that is,
   successfully call the pthread_rwlock_rdlock() function  n  times.  If
   so,  the  application  shall  ensure that the thread performs matching
   unlocks - that is, it  calls  the  pthread_rwlock_unlock()  function  n
   times.''')
endif

## ===========================================================================
## Check IPv6 addresss configuration
## ===========================================================================
conf.set10('HAVE_PROC_NET_IF_INET6', host_machine.system() == 'linux',
           description: 'Define to 1 if you have /proc/net/if_inet6 control file')

# ======================================================================
# SAC_OPENSSL
# ======================================================================

openssl_dep = dependency('OpenSSL', required: get_option('openssl'))
conf.set10('HAVE_OPENSSL', openssl_dep.found(),
           description: 'Define to 1 if you have OpenSSL')
conf.set10('HAVE_TLS', openssl_dep.found(),
           description: 'Define to 1 if you have TLS')

# ======================================================================
# SAC_TPORT - perform checks for tport
# ======================================================================

conf.set10('HAVE_SIGCOMP', false,
           description: 'Define to 1 if you have Sofia sigcomp >= 2.5')
conf.set10('HAVE_SOFIA_SIGCOMP', false,
           description: 'Define to 1 if you have Sofia sigcomp >= 2.5')
sigcomp = get_option('sigcomp')
if sigcomp == 'yes'
  cc.check_header('sigcomp.h', required: true)
  cc.has_function('sigcomp_library_2_5', args: '-lsigcomp', required: true)
  conf.set10('HAVE_SIGCOMP', true)
  conf.set10('HAVE_SOFIA_SIGCOMP', true)
elif sigcomp != ''
  cc.check_header(
    'sigcomp.h',
    include_directories: include_directories(get_option('sigcomp') / 'include'),
    required: true)
  cc.has_function(
    'sigcomp_library_2_5',
    args: [f'-L@sigcomp@/lib', '-lsigcomp'],
    required: true)
  conf.set10('HAVE_SIGCOMP', true)
  conf.set10('HAVE_SOFIA_SIGCOMP', true)
endif

# AC_SYS_IP_RECVERR
# ======================================================================
# Check for IP_RECVERR option
# ======================================================================
ac_cv_sys_ip_recverr = cc.compiles('''
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(void) {
  int one = 1;
  int s = 0;
  setsockopt(s, SOL_IP, IP_RECVERR, &one, sizeof(one));
  return 0;
}
''',
  name: 'IP_RECVERR')
conf.set10('HAVE_IP_RECVERR', ac_cv_sys_ip_recverr,
           description: 'Define to 1 if you have IP_RECVERR in <netinet/in.h>')

# AC_SYS_IPV6_RECVERR
# ======================================================================
# Check for IPV6_RECVERR option
# ======================================================================
ac_cv_sys_ipv6_recverr = cc.compiles('''
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main(void) {
  int one = 1;
  int s = 0;
  setsockopt(s, SOL_IPV6, IPV6_RECVERR, &one, sizeof(one));
  return 0;
}
''',
  name: 'IPV6_RECVERR')
conf.set10('HAVE_IPV6_RECVERR', ac_cv_sys_ipv6_recverr,
           description: 'Define to 1 if you have IPV6_RECVERR in <netinet/in6.h>')

foreach header: ['netinet/tcp.h', 'netinet/sctp.h']
  name = header.underscorify().to_upper()
  conf.set10(
    f'HAVE_@name@',
    cc.check_header(header, prefix: [
      '#include <sys/types.h>',
      '#include <sys/socket.h>',
    ])
  )
endforeach

conf.set10('HAVE_SCTP', get_option('sctp'),
           description: 'Define to 1 if you have SCTP')

# Check is used for testing
check_dep = dependency('check', version: '>= 0.9.4', required: false)
conf.set10('HAVE_CHECK', check_dep.found(),
           description: 'Define to 1 if check library is available')
conf.set10('HAVE_FNMATCH_H', cc.check_header('fnmatch.h'))

conf.set10(
  'HAVE_PTHREAD_SETSCHEDPARAM',
  cc.has_function('pthread_setschedparam', args: '-lpthread'),
  description: 'Define if you have pthread_setschedparam()')
zlib_dep = dependency('zlib', required: false)
conf.set('HAVE_ZLIB_COMPRESS', zlib_dep.found(),
         description: 'Define if you have zlib compress')

# dl is currently used only in testing
dl_dep = dependency('dl', required: false)
conf.set10('HAVE_LIBDL', dl_dep.found(),
           description: 'Define to 1 if dl library is available')

### internal modules
### ----------------
conf.set10('HAVE_SOFIA_SIP', true,
           description: 'Define to 1 always')
conf.set10('HAVE_SOFIA_SRESOLV', true,
           description: 'Define to 1 if we use DNS library')
conf.set10('HAVE_SOFIA_SMIME', false,
           description: 'Define to 1 if we use S/MIME library')

HAVE_STUN = false
if get_option('stun')
  if openssl_dep.found()
    # compile STUN only if OPENSSL is available
    HAVE_STUN = true
  else
    message('** TLS support for STUN disabled as OpenSSL headers and/or libraries were not found **')
  endif
endif
conf.set10('HAVE_SOFIA_STUN', HAVE_STUN,
           description: 'Define to 1 if we use STUN library')

HAVE_NTH = get_option('nth')
conf.set10('HAVE_SOFIA_NTH', HAVE_NTH,
           description: 'Define to 1 if we use NTH library')
conf.set10('HAVE_SOFIA_HTTP', HAVE_NTH,
           description: 'Define to 1 if we use HTTP parser library')

HAVE_NTLM = get_option('ntlm')
conf.set10('HAVE_SOFIA_NTLM', HAVE_NTLM,
           description: 'Define to 1 if we use NTLM library')

conf.set10('HAVE_SRTP', false,
           description: 'Define to 1 if we use SRTP')
conf.set10('HAVE_UPNP', false,
           description: 'Define to 1 if we use UPnP')

conf.set('HAVE_MEMLEAK_LOG', get_option('memleak_log'),
         description: 'Define to 1 for memory-leak-related logging')

### output
### ------

conf.set_quoted('PACKAGE_NAME', meson.project_name())
conf.set_quoted('PACKAGE_VERSION', meson.project_version())
conf.set_quoted('VERSION', meson.project_version())

configure_file(output: 'config.h', configuration: conf)

subdir('libsofia-sip-ua')
subdir('libsofia-sip-ua-glib', if_found: glib_dep)
# subdir('packages')
subdir('tests')
# subdir('s2check')
# subdir('utils')
# subdir('win32')
# subdir('open_c')
