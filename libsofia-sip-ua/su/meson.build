# meson.build for su module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

TAG_AWK = files('tag_dll.awk')

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

su_tag_ref_c = custom_target(
  input: 'su_tag.c',
  output: 'su_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libsu_SOURCES = [
  'su.c',
  'su_errno.c',
  'su_addrinfo.c',
  'su_alloc.c',
  'su_alloc_lock.c',
  'su_strdup.c',
  'su_sprintf.c',
  'su_strlst.c',
  'su_vector.c',
  'su_time.c',
  'su_time0.c',
  'su_wait.c',
  'su_root.c',
  'su_timer.c',
  'su_port.c',
  'su_port.h',
  'su_base_port.c',
  'su_pthread_port.c',
  'su_socket_port.c',
  'su_poll_port.c',
  'su_epoll_port.c',
  'su_select_port.c',
  'su_kqueue_port.c',
  'su_devpoll_port.c',
  'su_localinfo.c',
  'su_os_nw.c',
  'su_taglist.c',
  'su_tag.c',
  'su_tag_io.c',
  'su_log.c',
  'su_global_log.c',
  'su_default_log.c',
  'su_module_debug.h',
  'su_md5.c',
  'su_uniqueid.c',
  'su_bm.c',
  'smoothsort.c',
  'su_string.c',
  'string0.c',
]

if COREFOUNDATION
  libsu_SOURCES += ['su_osx_runloop.c']
endif

foreach func : ['memmem', 'memccpy', 'memspn', 'memcspn', 'strtoull', 'inet_ntop', 'inet_pton', 'poll']
  if conf.get('HAVE_' + func.to_upper()) == 0
    libsu_SOURCES += f'@func@.c'
  endif
endforeach

# ----------------------------------------------------------------------
# Build targets

libsu = static_library(
  'su',
  libsu_SOURCES,
  include_directories: toplevel_inc,
  dependencies: [pthread_dep],
)

libsofia_sip_ua_lib_deps += libsu

addrinfo = executable(
  'addrinfo',
  'addrinfo.c',
  include_directories: toplevel_inc,
  link_with: libsu,
  install: true)

localinfo = executable(
  'localinfo',
  'localinfo.c',
  include_directories: toplevel_inc,
  link_with: libsu,
  install: true)

# ----------------------------------------------------------------------
# Tests

su_tests = [
  'test_htable',
  'test_htable2',
  'test_memmem',
  'test_poll',
  'torture_heap',
  'torture_rbtree',
  'torture_su',
  'torture_su_alloc',
  'torture_su_bm',
  'torture_su_port',
  'torture_su_root',
  'torture_su_tag',
  'torture_su_time',
  'torture_su_timer',
]
if COREFOUNDATION
  su_tests += [
    'torture_su_root_osx',
  ]
endif
foreach name : su_tests
  test_exe = executable(
    name, f'@name@.c',
    include_directories: toplevel_inc,
    link_with: libsu,
  )
  test(
    name, test_exe,
    suite: name.startswith('torture') ? ['su', 'torture'] : ['su'])
endforeach

# Test addrinfo with -n and echo port on 127.0.0.1
test(
  'addrinfo',
  addrinfo, args: ['-n', 'echo', '127.0.0.1'],
  suite: 'su')
# Test localinfo with -n flag
test(
  'localinfo',
  localinfo, args: ['-n'],
  suite: 'su')

test_su = executable(
  'test_su', 'test_su.c',
  include_directories: toplevel_inc,
  link_with: libsu,
)
su_proxy = executable(
  'su_proxy', 'su_proxy.c',
  include_directories: toplevel_inc,
  link_with: libsu,
)
foreach SU_PORT : ['select', 'kqueue', 'devpoll', 'epoll', 'poll']
  func_name = 'HAVE_' + SU_PORT.to_upper()
  header_name = 'HAVE_' + SU_PORT.to_upper() + '_H'
  sys_header_name = 'HAVE_SYS_' + SU_PORT.to_upper() + '_H'
  if conf.get(func_name, 0) == 0 and conf.get(header_name, 0) == 0 and conf.get(sys_header_name, 0) == 0
    continue
  endif

  test(
    f'test_su-multithread-@SU_PORT@', test_su,
    env: {'SU_PORT': SU_PORT},
    suite: 'su')
  test(
    f'test_su-singlethread-@SU_PORT@', test_su, args: ['-s'],
    env: {'SU_PORT': SU_PORT},
    suite: 'su')
endforeach

if COREFOUNDATION
  test_su_osx = executable(
    'test_su_osx', 'test_su_osx.c',
    include_directories: toplevel_inc,
    link_with: libsu,
  )

  test(
    'test_su_osx-multithread', test_su_osx,
    env: {'SU_PORT': 'CoreFoundation'},
    suite: 'su')
  test(
    'test_su_osx-singlethread', test_su_osx, args: ['-s'],
    env: {'SU_PORT': 'CoreFoundation'},
    suite: 'su')
endif
