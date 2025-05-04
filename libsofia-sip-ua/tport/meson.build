# meson.build for tport module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

tport_tag_ref_c = custom_target(
  input: 'tport_tag.c',
  output: 'tport_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libtport_SOURCES = [
  'tport.c',
  'tport_logging.c',
  'tport_stub_sigcomp.c',
  'tport_type_udp.c',
  'tport_type_tcp.c',
  'tport_type_sctp.c',
  'tport_internal.h',
  'tport_tag.c',
  tport_tag_ref_c
]
if openssl_dep.found()
  libtport_SOURCES += [
    'tport_type_tls.c',
    'tport_tls.c',
    'tport_tls.h',
  ]
endif

if HAVE_STUN
  libtport_SOURCES += [
    'tport_stub_stun.c',
    'tport_type_stun.c',
  ]
endif

if HAVE_NTH
  libtport_SOURCES += [
    'tport_type_connect.c',
    'tport_type_ws.c',
    'ws.c',
  ]
endif

# ----------------------------------------------------------------------
# Build targets

libtport = static_library(
  'tport',
  libtport_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [
    toplevel_inc, libsofia_sip_ua_inc,
    include_directories('include')],
)

libsofia_sip_ua_lib_deps += libtport

# ----------------------------------------------------------------------
# Tests

test_tport = executable(
  'test_tport',
  'test_tport.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libtport, libstun, libsresolv, libipt, libhttp, libtest_msg, libmsg, liburl, libbnf, libsu],
  dependencies: [openssl_dep])
test('test_tport', test_tport,
     env: {'srcdir': meson.current_source_dir()},
     suite: 'tport')
