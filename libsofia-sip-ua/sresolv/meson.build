# meson.build for sresolv module
#
# Copyright (C) 2025 Elliott Sales de Andrade
# Licensed under LGPL. See file COPYING.

# ----------------------------------------------------------------------
# Headers

subdir('sofia-sip')
subdir('sofia-resolv')

# ----------------------------------------------------------------------
# Rules for building the targets

libsresolv_SOURCES = [
  'sres.c',
  'sres_cache.c',
  'sres_blocking.c',
  'sresolv.c',
  'sres_sip.c',
]

# ----------------------------------------------------------------------
# Build targets

libsresolv = static_library(
  'sresolv',
  libsresolv_SOURCES,
  include_directories: [toplevel_inc, libsofia_sip_ua_inc],
)

libsofia_sip_ua_lib_deps += libsresolv

# ----------------------------------------------------------------------
# Tests

# Requires url, which hasn't been built yet, so skip these.
# test_sresolv = executable(
#   'test_sresolv',
#   'test_sresolv.c',
#   include_directories: [toplevel_inc, libsofia_sip_ua_inc],
#   link_with: [libsresolv, liburl, libbnf, libsu])
# test('test_sresolv', test_sresolv, suite: 'sresolv')

# Requires url, which hasn't been built yet, so skip these.
# torture_sresolv = executable(
#   'torture_sresolv',
#   'torture_sresolv.c',
#   include_directories: [toplevel_inc, libsofia_sip_ua_inc],
#   link_with: [libsresolv, liburl, libbnf, libsu])
# test('torture_sresolv', torture_sresolv, suite: ['sresolv', 'torture'])

s2check_tests += {
  'check_sres_sip': {
    'sources': files('check_sres_sip.c'),
    'include_directories': toplevel_inc,
    'dependencies': check_dep,
  },
}
