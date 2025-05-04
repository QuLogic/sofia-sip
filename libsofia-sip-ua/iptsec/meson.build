# meson.build for iptsec module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

auth_tag_ref_c = custom_target(
  input: 'auth_tag.c',
  output: 'auth_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libiptsec_SOURCES = [
  'iptsec_debug.h',
  'auth_client.c',
  'auth_common.c',
  'auth_digest.c',
  'auth_module.c',
  'auth_tag.c',
  auth_tag_ref_c,
  'auth_plugin.c',
  'auth_plugin_delayed.c',
  'auth_module_sip.c',
  'iptsec_debug.c',
]

if HAVE_NTLM
  libiptsec_SOURCES += [
    'auth_ntlm.c',
    'auth_client_ntlm.c',
    'auth_plugin_ntlm.c',
  ]
endif

if HAVE_NTH
  libiptsec_SOURCES += [
    'auth_module_http.c',
  ]
endif

# ----------------------------------------------------------------------
# Build targets

libiptsec = static_library(
  'iptsec',
  libiptsec_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libiptsec

# ----------------------------------------------------------------------
# Tests

test_auth_digest = executable(
  'test_auth_digest',
  'test_auth_digest.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libiptsec, libnta, libsip, libmsg, liburl, libbnf, libipt, libsu])
test('test_auth_digest', test_auth_digest,
     env: {'srcdir': meson.current_source_dir()},
     suite: 'iptsec')
