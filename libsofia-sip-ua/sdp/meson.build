# meson.build for sdp module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

sdp_tag_ref_c = custom_target(
  input: 'sdp_tag.c',
  output: 'sdp_tag_ref.c',
  command: [AWK, '-f', TAG_AWK, 'NODLL=1', 'REF=@OUTPUT@', '@INPUT@'])

libsdp_SOURCES = [
  'sdp.c',
  'sdp_parse.c',
  'sdp_print.c',
  'sdp_tag.c',
  sdp_tag_ref_c,
]

# ----------------------------------------------------------------------
# Build targets

libsdp = static_library(
  'sdp',
  libsdp_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libsdp

# ----------------------------------------------------------------------
# Tests

torture_sdp = executable(
  'torture_sdp', 'torture_sdp.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsdp, libsu])
test('torture_sdp', torture_sdp, suite: ['sdp', 'torture'])

test_sdp = executable(
  'test_sdp', 'test_sdp.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libsdp, libsu])
test('test_sdp',
     find_program('run_test_sdp'), args: test_sdp,
     suite: 'sdp')
