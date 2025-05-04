# meson.build for nth module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

nth_tag_ref_c = custom_target(
  input: 'nth_tag.c',
  output: 'nth_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libnth_SOURCES = [
  'nth_client.c',
  'nth_server.c',
  'nth_tag.c',
  nth_tag_ref_c
]

# ----------------------------------------------------------------------
# Build targets

libnth = static_library(
  'nth',
  libnth_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libnth

# ----------------------------------------------------------------------
# Tests

# This depends on libiptsec, which is created after nth, so cannot be done right now.
# test_nth = executable(
#   'test_nth',
#   'test_nth.c',
#   include_directories: [toplevel_inc, libsofia_sip_ua_inc],
#   link_with: [libnth, libiptsec, libipt, libhttp, libsresolv, libtport, libstun, libsresolv, liburl, libmsg, libbnf, libsu])
# test('test_nth', test_nth)
