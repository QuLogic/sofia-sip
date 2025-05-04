# meson.build for bnf module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

libbnf_SOURCES = [
  'bnf.c',
]

# ----------------------------------------------------------------------
# Build targets

libbnf = static_library(
  'bnf',
  libbnf_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libbnf

# ----------------------------------------------------------------------
# Tests

torture_bnf = executable(
  'torture_bnf',
  'torture_bnf.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: [libbnf, libsu])
test('torture_bnf', torture_bnf, suite: ['bnf', 'torture'])
