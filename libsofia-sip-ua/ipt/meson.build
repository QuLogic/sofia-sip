# meson.build for ipt module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')

# ----------------------------------------------------------------------
# Rules for building the targets

libipt_SOURCES = [
  'base64.c',
  'token64.c',
]

# ----------------------------------------------------------------------
# Build targets

libipt = static_library(
  'ipt',
  libipt_SOURCES,
  c_args: '-DIN_LIBSOFIA_SIP_UA',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libipt

# ----------------------------------------------------------------------
# Tests

torture_base64 = executable(
  'torture_base64',
  'torture_base64.c',
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
  link_with: libipt)
test('torture_base64', torture_base64, suite: ['ipt', 'torture'])
